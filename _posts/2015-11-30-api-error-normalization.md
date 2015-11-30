---
layout: post
title: API error normalization
description: the importance of normalizing the possible errors of the App
tags: api rails json
---

When building an API a typical decision to make is how to treat errors, understanding that errors are responses within the 400 and 500 ranges for status code, first and foremost, the responses status code should be semantic to give a standard understanding of the type of error according to HTTP.

Apart from that errors should help client developers to understand what actions to take in order to make a successful request, is it client or server fault? What kind of error is it? Why it happened? How I can prevent this? (if I can).

Considering that, errors response bodies should be able to answer to all those questions, even more, present that information in a consistent way to make the parsing/extracting of the error more easy.

Error normalization is important because it's all about debugging and support :).

In the current project I am working on at [Hyper][hyper], the API we are building for the client developers, has normalized error responses that look like follows.

Given the example request

```
POST https://backend.com/api/cat_videos
```

Which will create a new `CatVideo` resource to our backend, can go wrong in many ways:

- `400 bad request`: the client didn't give the default parameters/headers in order to make the request.
- `401 unauthorized`: the client did not provide the authentication information.
- `403 forbidden`: the client is authenticated, but doesn't have permissions to access to this feature.
- `422 unprocessable entity`: the client data does not fulfill the system validations.
- `500 internal server error`: the backend developers did something wrong x.X
- `503 service unavailable`: the server is possibly down or experiencing timeouts.

So let's take the case of a `422` error, the response body would look like this:

```json
{
  "error": {
    "status":  422,
    "id":      "unprocessable_entity",
    "message": "The given params are not valid",
    "documentation_url": "https://api.com/docs/cat_videos",
    "validations": {
      "title": [
        "can't be blank"
      ]
    }
  }
}
```

As you can see I use some properties on the payload that are:

- **status**: the HTTP status code of the response.
- **id**: a string based http status code or a unique kind of error associated with that status code (like `invalid_credentials` with a `401` code).  Interestingly, you can add custom `ids` here, in order to label specific kinds of errors in your business domain.
- **message**: a more human friendly message for the given kind of error, which can explain for example, the need of a required parameter (`400` response).
- **documentation_url**: a link informing where the developers can find the right documentation for that endpoint.
- **validations** _(optional)_: this is specific for a `422` response, which will include the right validation messages, mostly extracted from Rails.

It's also worth noting that for **message** that string can be in any language your application uses, I commonly use the `Accept-Language` header in order to be able to expose the human-friendly error messages in the specified language.

So all possible errors given by the API will have this structure, then common logic can be expressed as a rails controller concern like this.

<script src="https://gist.github.com/fespinoza/3c75877aedccef709dde.js"></script>

[hyper]: http://www.hyper.no

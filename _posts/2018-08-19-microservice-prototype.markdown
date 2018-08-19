---
layout: post
title: Microservice Prototype
date: 2018-08-19 00:00:00 +0300
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
img: workflow.jpg # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [Productivity, Workflow] # add tag

---

## Service starting sequence

- [ ] Start eureka first
- [ ] Then start config server
- [ ] Start other servies

### JWT token claim lib decisions

> As spring security has many limitations for the JWT token claiming, so here we choose a very popular lib  - JJTW to generate tokens, and we can customize the token whatever we want.
>
> Link: https://github.com/jwtk/jjwt#jws-create-key
>
> If you want to test it, please use the auth service, you can use  below API to generate the token, which is a POST request, and use the post body as below:
>
> #### Post Body:
>
> {
>     "username":"Alex",
>     "emailAddress":"lixiaobo@cn.ibm.com"
> }
>
> #### Post Header:
>
> Content-type: application/json
>
> #### Post Url:
>
> http://localhost:8001/v1/auth/generatetoken

> ```java
> 	@ResponseBody
> 	@RequestMapping(value = "/{version}/auth/generatetoken", method = RequestMethod.POST)
>     public String generateToken(@PathVariable("version") String version,
>     		@RequestBody @Valid AccessTokenPayload payload) throws Exception {
> 		HashMap<String, Object> map = mObjectMapper.readValue(mObjectMapper.writeValueAsString(payload), new TypeReference<HashMap<String, Object>>() {
>         });
> 		return mTokenImpl.createToken(map, Util.getNotBeforeDate(30));
>     }
> ```
>
> 

### Service and port mapping

| Project                  | Service Name    | Port |
| ------------------------ | --------------- | ---- |
| Auth Service             | auth-service    | 8001 |
| Zuul Gateway Service     | gateway         | 8002 |
| Eureka Discovery Service | discovery       | 8761 |
| Config Service           | config-server   | 8888 |
| WebApp Service           | webapp          | 8003 |
| Content Service          | content-service | 9001 |
| Service A                | service-a       | 9002 |

### Common Service

> This is not a run time service, it will only be used as common library. As we will check the tokens in zuul and webapp, so there will be many duplicated code. And in the future, we may have some more resuable code. 

### How to test the service which needs authentication and behind zuul

1. Prepare a token with auth-service API provided above

2. Please start Eureka, Config Server, Zuul, Service A

3. Test sevice: http://localhost:8002/v1/api/platform/platformlist

   1) Add a header:

   Authorization:token - the token in step 1

   Expect result: Get date from platform list api

   2) Remove the header

   Expect result: Invalid Access Token

   ​

   ​
# Single Page Apps

## What an API developer should know

_PyDataDC, Nov. 17, 2018, Alison Rowland_
---

## Things Are Different Here

Note:
- A message from front-end land
- 10+ yrs BE, 8 mos FE
- wrap head around

---

## Who Should Listen to this Talk

### Builders of:

- Read/Write APIs
- APIs which power client applications 

Note:
- This talk is written about SPAs, but the principles may apply similarly to mobile app development

---

## Why Should I Learn More About SPAs

@ul
- architecture
- security
- team communication
- choice of stack
@ulend

---

# Poll #1

Note:
- Raise your hand if you've written a single page app.

---

# Poll #2

Note:
- Now I'd like everybody to raise their hand.
- Leave your hand up if:
  - You've written an API
  - You've written an API that backs a single page app
  - You've written an API that backs a single page app with user logins
  - You've written an API platform that backs multiple different single page apps with user logins

---

## Agenda

1. SPA Architecture
2. Security
3. State
4. The Upshot of Being Static
5. SPA Back Ends
6. Why?

---

# 1/6 SPA Architecture

---

# SPA Frameworks

- Angular
- Vue
- React
- Ember
- etc...

Note:
- These are mostly similar, though Vue and React offer server-side rendering (or isomorphic JS), which muddies the water a bit.

---

# SSR vs CSR

Note:
- Highest level, difference between preparing your HTML server-side or client-side

---

# SPAS:
- 1 request for templates
- subsequent requests: data-only

Note:
- We'll see this illustrated in the next slide.

---

Source: https://msdn.microsoft.com/
![SPA Request Anatomy](assets/img/spa-pro-con.png)

---

# Deployment

## Totally Static!

Note:
- It's possible to host an SPA on a completely unadorned S3 bucket
- This was one of the hardest concepts for other devs to understand
- We'll be elaborating on the ramifications of this through the rest of the talk.

---

## What the Hashtag Does
# #

Note:
- A (standard) SPA only has a single route on the server

---

# One Route

## http://yoursite.com/

^ This downloads the static app bundle

---

# JS Routing

- http://yoursite.com/#products
- http://yoursite.com/#about
- http://theirsite.com/#/store/items/1
- http://theirsite.com/#/blog/posts/2018-pydatadc-rocks

Note:
- Everything after the hashtag is routed by Javascript, in the browser.
- Additional hits to get data, such as from your API, will be done via XMLHttpRequest
- There can be variations to how the URL is formatted, such as a slash right after the hashtag

---

# 2/6 Security

---

## Never Trust the Client

---?image=assets/img/obama-door.jpg&size=contain&opacity=50

## YOU OWN SECURITY
### IN the API

---?image=assets/img/ben-franklin-key.jpg&size=contain&opacity=50

## User Owns Authentication

Note:
- client can't hold secrets
- user's valid username/password pair are everything
- user will receive a session token after they authenticate, then pass that back to your API

---?image=assets/img/bridge-tower.jpg&size=contain&opacity=50

## API Owns Authorization

Note:

- you can only trust the user
- can't trust session key for authorization
- can't trust client for authorization

---

## Double-Check Permissions
### Session -> User -> Permissions
### ! Session -> Permissions

Note:
- stating this another way
- permissions belong to the user object
- don't rely on the session key

---?image=assets/img/id-mclovin.jpg&size=contain&opacity=50
@transition[none]

@snap[north-west]
## authorize real-time for:

- elevated permissions
- destructive actions


Note:
- things can happen in real-time that make sessions or permissions invalid, such as a user's account entering fraud status
- all elevated privilege or destructive actions must be authorized or re-authorized in real-time

---

## Client-Based Permissions?!

Vertical A, Vertical B, Administrative

(lions, tigers and bears, oh my!)

Note:
- (possible but tricky)
- Let's say you've built an Identity platform, and you have clients to serve different verticals.
- One could be for banking, one could be for the DMV.
- A given user can authorize a client on their account
- But a malicious user can abuse those privileges to perform actions that are not supposed to happen from that CLIENT
- How do you differentiate the users and their permissions per client, if you can't trust the client?
- One option is forcing multiple user accounts for different permissions, but that kind of defeats the purpose of your identity platform.
- Don't have a good answer...

---

# CORS / XSRF

Every user request is now cross-domain.

Note:
- Final note on security...
- In addition to the user's auth token
- your API will need to support XSRF tokens to protect against cross-site scripting attacks.

---

> If tokens aren’t enough, why do so many public APIs only provide token-based authentication? Probably because the primary use case for these APIs isn’t to be consumed directly by a JS client in a web browser. [..] Are you sure your apps are so secure?

_-- François Zaninotto, "Your API-Centric Web App Is Probably Not Safe Against XSS and CSRF", Nov 2015_

http://www.redotheweb.com/2015/11/09/api-security.html

Note:
- I found this quote from a really great article on SPAs and why they need cross-site scripting protections.

---

# 3/6 State

---?image=assets/img/paris-opera-house.jpg&opacity=50&size=contain

## Your API is not a Museum

Note:
- Unless read-only...
- I've been guilty of this attitude myself...

---?image=assets/img/bazaar2.jpg&opacity=50&size=contain

## Your API Is a Bazaar


@snap[south]
@size[.5em](Source: https://wellcomecollection.org/works/um7buv3a)

Note:
- Unless your api is R-only
- Needs to be able to save state, and possibly even transactional histories, have object versioning, audit logs, etc.

---

## Poll

Note:
- raise your hand if you develop read-only APIs
- "Don't change jobs-- it's nice work if you can get it"

---

## Client State
### Necessary but Limited

Note:
- Client state can't persist across sessions or device
- Might be "duh", but it's important to consider

---

## Client State
### REALLY not a museum

Note:
- Think carefully about how client interactions should or should not change the core data
- Be prepared to represent client state differently than your "core" data
- e.g. in other models, other microservices, etc.

---?image=assets/img/hydrant.jpg&size=contain&opacity=40

## UX
### of persistence
### and refresh

Note:
- consider what data may change in your API that the user needs to know about or app needs to respond to
- how will the user get that data?
- unless you're implementing websockets, updates will be based on polling or strategic refresh
- some data can be fetched and persisted at login, others with each page load, others after certain more granular interactions

---?image=assets/img/dancers-mirror.jpg&size=contain&opacity=40

@snap[north-west]
## Your data model
## mirrored in the app

Note:
- modern JS frameworks are built on the idea that the client app is independent
- especially true under the Redux or RXJS model.
- they need to implement a working and independent data model themselves

---

## similar but non-identical data model

X -> Y -> Y1 -> X1

(API) -> (client) -> (API)

Note:
- the shape of data the client needs may not be the shape your API produces
- As a result, getting data out may become easy, but getting it back in will often become harder. Objects transformed for display will need to be un-transformed to be persisted.
- Every time the schema of X changes in your API, Y, in the client, may need to change its schema as well.
- talk to your FE and Edge developers -- their experience (re)building your data model and endpoints can inform a better API design

---

# 4/6 The Upshot of Being Static

---?image=assets/img/duck-lady.jpg&size=contain&opacity=40

## BUT WHAT DOES IT ALL MEAN

Note:
- I'd like to step back and look at the ramifications of all this

---?image=assets/img/little-teacher.jpg&size=contain&opacity=40

## Review

---?image=assets/img/snow-plow.jpg&size=contain&opacity=30

### A URL refers to the whole of an SPA

`https://yoursite.com/#/.......`

Note:
- This is really difficult for many server-side devs to understand
- Any URL referring to something _inside_ the app, beyond the front page, is called a "deep link."
- Deep-linking is possible but often tricky/annoying to implement, because SPAs are often built for people to come "in the front door," so to speak

---?image=assets/img/mcpherson-statue.jpg&size=contain&opacity=40

## SPAs are static
### Plus logic and ajax

---

## Your API layer is exposed to the world

Note:
- Because of that logic and ajax, your API can't hide behind server-side rendering

---?image=assets/img/washdc-map.jpg&size=contain&opacity=40

## All experiences must be baked-in

Note:
- you can't push a new experience or flow to the user based on data/circumstance/state
- it has to be there from the beginning
- and by extension of that...

---?image=assets/img/glacier.jpg&size=contain&opacity=40

## Sensitive Data
## Must be Preserved in the API

Note:
- Data you might not think of as sensitive, but which is data which a given user might not be privy to needs to be held on the server side
- Since all possibile user-flows (but not all possible related data) must be baked into the client
- Example: third-party terms and conditions. OR Sensitive error messages about someone's account status. (You may not want to expose all possible error states to the world.)
- This hearkens back to the security portion of this talk-- separating business logic, and all possible permutations of that, as well as sensitive data, requires a different way of thinking than you may be used to.
---

# 5/6 SPA Back Ends

Note:
- There are some solutions that we can turn to to make all of this a little easier

---

## Edge APIs

@ul
- expose
- proxy
- orchestrate
@ulend

Note:
- We've mentioned exposing your API to the world... an Edge sits in front of your key systems and...
- (click thru and read list)
- Edge APIs can also represent somewhat of a challenge for your dev teams, because it's another layer of abstraction to deal with
- almost always used for mobile apps

---?image=assets/img/bank-safe.jpg&size=contain&opacity=25

## An Edge API can hold credentials for:

- specific clients (careful)
- APIs you don't own

Note:
- still possible for a rogue client to abuse an edge beyond its intended purpose
- but an Edge can be your only way to keep certain things secure

---

## SSR
### viewable sooner, interactable later

Helps with:
@ul
- SEO
- performance
@ulend

Note:
- Vue and React support SSR

---

## SSR

Doesn't help with:
@ul
- Security
- State
@ulend

---?image=assets/img/mirror.jpg&size=contain&opacity=50


# 6/6 Why?

Note:
- Why should I bother to understand all this?

---

## Architecture Decisions

Note:
- selecting stack
- overall architecture
- API request and schema architecture
- authentication
- security

---?image=assets/img/thermometer.jpg&size=contain&opacity=30

@snap[north]
## Know what you're building

@ul
- Which parts of your API are general-purpose?
- Tailored to a given client experience?
- Where do the two overlap?
- What data do you need to protect?
@ulend

Note:
- Figure out some guiding principles for how you want things to work, and make your team live by them, iterating as needed.

---?image=assets/img/masons.jpg&size=contain&opacity=30

## API Versioning

Consider the additive-only approach.

Note:
- When you're building an API to a client, coupling can become very tight.
- This is a contentious topic... Versioning in headers vs URLs...
- For maximum agility and least code-debt (having to support multiple versions in both back-end and client code), consider an additive-only approach.
- Backward-incompatible changes must be made to new endpoints. Old endpoints only removed once no-one is using them anymore.

---

## Security

### Understanding SPA architecture is key to securing your app

Note:
- In case I didn't harp on it enough, just a reminder that we really have to understand how SPAs function
- in order to properly secure our apps.
- I have a suspicion that the world is full of insecure SPAs and consequently insecure APIs
- 

---?image=assets/img/teams-horses.jpg&size=contain&opacity=50

## Org & Process
### Avoid Functional Silos

Note:
- If you're developing APIs and SPAs in a large, enterprise environment,
- the stack separation makes it easier to develop functional silos
- So I'd like to propose some considerations to counteract functional silos

---?image=assets/img/what-is-client.jpg&size=contain&opacity=50

## What Is a Client?

Note:
- I've said the word "client" a bunch here. I was using it in the computing sense of a piece of software which accesses a server.
- But I'd like to propose an alternate way to think about it........

---?image=assets/img/customer-clerk.jpg&size=contain&opacity=50

## Front-End Team is your client

Note:
- their needs will drive much of what you build
- listen to them carefully
- front-end lead is de facto solutions architect

---

## Communicate Around Verticals

Note:
- your org may be horizontal or functionally oriented,
- becomes more important to communicate about features

---?image=assets/img/seq-diag.png&size=contain&opacity=10

@snap[south]
## pictures and diagrams
draw them. ask for them frequently.

Note:
- a sequence diagram is worth 1,000 words

---

## Conclusion

### Hey, API developers!

Understand SPAs for:

@ul
- better architecture decisions
- better security
- better state management
- better engineering process management
@ulend

Note:
- Hey, API developers!
- Yes, this talk is still about you. This is about US.
- (read list)

---

## Thanks!

### Alison Rowland | @arowla @fa[twitter] @fa[github]
#### Director of Engineering | Breakout Capital Finance

@snap[south]
https://gitpitch.com/arowla/single-page-app-tips-for-api-devs/

Note:
- Thanks, and happy building!
- At the bottom there is the URL for these slides
- I work at a fintech company...
- My team is hiring, come find me later if you're interested in business automation with data pipelines, ML and AI.


<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">


# Single Page Apps

## What an API developer should know

_DC API Meetup, Sept. 4 2018, Alison Rowland_
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

---

## Agenda

1. Security
2. Persistence & State
3. The Upshot of Being Static
4. Edge APIs
5. SPA/API Development: HOWTO

---

# 1/5 Security

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
- all elevated privilege or destructive actions must be authorized in real-time

---

## Client-Based Permissions
### Possible (sort-of) but Tricky

Note:
- A given user can authorize a client on their account
- But a malicious user can abuse those privileges

---

# 2/5 Persistence
# & State

---?image=assets/img/paris-opera-house.jpg&opacity=50&size=contain

## Your API is not a Museum

Note:
- Unless read-only...
- I've been guilty of this attitude myself...

---?image=assets/img/bazaar2.jpg&opacity=50&size=contain

## Your API Is a Bazaar


@size[.5em](Source: https://wellcomecollection.org/works/um7buv3a)

Note:
- Unless your api is R-only
- Needs to be able to save state, and possibly even transactional histories, have object versioning, audit logs, etc.

---

## Client State

Note:
- Clients can persist things, but not across sessions or devices
- Think carefully about how client interactions should or should not change the core data
- Be prepared to represent client state differently, in other models, other microservices, etc.

---?image=assets/img/hydrant.jpg&size=contain&opacity=50

## UX drives persistence and refresh

Note:
- consider what data may change in your API that the user needs to know about or app needs to respond to
- how will the user get that data?
- unless you're implementing websockets, updates will be based on polling or strategic refresh
- some data can be fetched and persisted at login, others with each page load, others after certain more granular interactions

---?image=assets/img/masons.jpg&size=contain&opacity=50

## API Versioning

Consider the additive-only approach.

Note:
- This is a contentious topic... Versioning in headers vs URLs...
- For maximum agility and least code-debt (having to support multiple versions in both back-end and client code), consider an additive-only approach. Backward-incompatible changes must be made to new endpoints. Old endpoints only removed once no-one is using them anymore.

---

# 3/5 The Upshot of Being Static

---

## A URL refers to the whole of an SPA

`https://yoursite.com/#/.......`

Note:
- This is really difficult for many server-side devs to understand
- Anything URL referring to something _inside_ the app, beyond the front page, is called a "deep link."
- These are possible but often tricky/annoying to implement

---?image=assets/img/mcpherson-statue.jpg&size=contain&opacity=50

## SPAs are static
## + logic and ajax

---?image=assets/img/washdc-map.jpg&size=contain&opacity=50

## All experiences must be baked-in

Note:
- you can't push a new experience or flow to the user based on data/circumstance/state
- it has to be there from the beginning
- and by extension of that...

---?image=assets/img/glacier.jpg&size=contain&opacity=50

## Sensitive Data
## Must be Preserved in the API

Note:
- Data you might not think of as sensitive, but which is data which a given user might not be privy to needs to be held on the server side
- Since all possibile user-flows (but not all possible related data) must be baked into the client
- Example: third-party terms and conditions. OR Sensitive error messages about someone's account status. (You may not want to expose all possible error states to the world.)
- This hearkens back to the security portion of this talk-- separating business logic, and all possible permutations of that, as well as sensitive data, requires a different way of thinking than you may be used to.
---

# 4/5 Edge APIs

---

## Edge APIs

- expose
- proxy
- orchestrate

Note:
- Edge APIs can also represent somewhat of a 

---?image=assets/img/bank-safe.jpg&size=contain&opacity=40

## An Edge API can hold credentials for:

- specific clients (careful)
- APIs you don't own

Note:
- still possible for a rogue client to abuse an edge beyond its intended purpose
- but an Edge can be your only way to keep certain things secure

---?image=assets/img/mirror.jpg&size=contain&opacity=50

## Your data model
## will get mirrored in the app

Note:
- modern JS frameworks are built on the idea that the client app is independent
- so they need to implement a working data model themselves
- Edge APIs will paper over some, but not all, of the complexity.
- As a result, getting data out may become easy, but getting it back in will often become harder. Objects transformed for display will need to be un-transformed to be persisted.
- talk to your FE and Edge developers -- their experience (re)building your data model and endpoints can inform a better API design

---

# 5/5 SPA/API Development: HOWTO

---?image=assets/img/teams-horses.jpg&size=contain&opacity=50

## Multiple teams at work
### How do you get it all done?

Note:
- _segue slide_

---

## Know what you're building

@ul
- Which parts of your API are general-purpose?
- Which parts are tailored to a given client experience?
- Where do the two overlap?
- What data do you need to protect?
@ulend

Note:
- Figure out some guiding principles for how you want things to work, and make your team live by them, iterating as needed.

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

## pop-up
## cross-stack
## feature teams

---?image=assets/img/seq-diag.png&size=contain&opacity=40

## pictures and diagrams
### draw them. ask for them frequently.

Note:
- a sequence diagram is worth 1,000 words

---

## Alison Rowland

@arowla

These slides:
`https://gitpitch.com/arowla/single-page-app-tips-for-api-devs/`




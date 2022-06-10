We assessed commit `#abcd134`

# Findings

## 1. SQL Injection

### Description

Describe some stuff.

### Recommendation

Recommend some stuff.

## 2. Insecure Direct Object Reference (IDOR)

### Description

Describe some stuff.

### Recommendation

Recommend some stuff.

---

# Notes for you/your team

## Behavior

* What does it do? (business purpose)
	* task/project manager app

* Who does it do this for? (internal / external customer base)
  * Appears to do both - elevates the risk of a compromise affecting more than just customer data.

* What kind of information will it hold? Which feilds have sensitive data?
  * Looks like Projects -> Tasks -> Notes
    * Sensitive information stores in projects and notes?
  * Date of Birth info - sensitive info stored for users.

* What are the different types of roles?
  * Admins, Team members, and project manager
  * Note that our seed data show `is_superuser` and `is_staff` role as well.

* What aspects concern your client/customer/staff the most?

## Tech Stack

* Framework & Language - Rails/Ruby, Django/Python, mux/Golang, templating engine
  * Django - 
  * Python 3


* 3rd party components, Examples:
  * Building libraries (rubygem, npm, jar, etc.)
  * JavaScript widgets - (marketing tracking, sales chat widget)
  * Reliant upon other applications - such as receiving webhook events
  * Data parsing librarries

  * xlwt - looks like perhaps we-re doing some exce/csv type stuff (think about csv injection)
  * Static JS, JQuery, Bootstrap (is outdated)
  * Django.contrib.admin is used for administrative functionality

* Datastore - Postgresql, MySQL, Memcache, Redis, Mongodb, etc.
    * MySQL

## Brainstorming / Risks
collaborative brainstorming improves risks, the better this list will be. (diversity and thought)

* Here is what the feature or product is supposed to do... what might go wrong?
* Okay - based on the tech stack, I've realized that the:
  * ORM - Does SQLi in _this_ way
  * Template language introduces XSS in _this_ way
* MySQL
  * SQL Injection
* Noted that use of what looks like MD5 in nthe seed data for users.json - check on MD5 usage.
* Looks like there are  user profile images that can be either uploaded/downloaded (probably by users themselves)
  * There is an upload folder under static assets which is weird because normally that is a folder for unautho access.
* Think about CSV injection and spreadsheet generation (xlwt)
* Sensitive data with DoB (look for other things) - think about it in transit / rest.
* Django version is out of date - look up for vulns
---

* VTM nginx - shows some interesting things. Let's look at that configuration.
* Static JS / JQuery / Bootstrap is old.
* Font vulns that followed after JQuery vulns
* Looks to be some juicy debug endpoints we should checkout.
* OS level commands in technology stack (python)


## Checklist of things to review

### Risks
- [ ] Look for instances of `| safe` in the template/views
- [ ] Look for OS commands
- [ ] Look at the ORM for instances of `createNativeQuery()`
- [ ] Developer expected `x` but I think we should try to see if `y` is possible

- [ ] .raw(), .execute()
- [ ] `settings.py` and models maybe - look at MD5 usage
- [ ] Find where uploads/downloads are happening (processing/access of files):
  - [ ] Size
  - [ ] Content / MIME type
  - [ ] Zip/Tar?
  - [ ] Permissioning/Authorization
  - [ ] Location? Policy around that?
- [ ] CSV Injection - look at validations / escaping routines
- [ ] Look at how DoB is stored/processed (also, anything else sentivie?)
- [ ] Lookup vulns in the various libs (including django) used by the app
- [ ] Analyze ourr JQuery usage - probably out of date.
- [ ] Look at and analyze the nginx config where we have allow origins wildcards stuff going on.
- [ ] Checkout JQuery/Bootstrap version for vulns
- [ ] Make sure @login_required is used sanely
- [ ] import `os`, `subprocess`
- [ ] need to look at unsafe html operations in this template language - django unsafe html methods.

### Authentication
- [ ] Login page give error messages, check for enumeration
- [ ] Signup page allows for freeform passwords, does it implement proper password complexity?

### Authorization
- [ ] Uses @login_required decorator, is it applied on all endpoints appropriately?

- [ ] `@login_required` - built in django decorator - only checks for session but not true authorization.
  * Interesting - it papears this decorator is stacked on top of user_passes_test - is that necessary? Is that a pattern we should repeat?
- [ ] `@user_passes_test`

### Auditing/Logging
- [ ] Logging configuration is in `settings.py`, check documentation for secure settings

### Injection
- [ ] ORM `where` function allows for string concatenation, search for all instances

### Cryptography
- [ ] References to base64 when handling passwords, is this bad?

### Configuration
- [ ] Code is ruby/rails, make sure and run brakeman before closing out

## Mapping / Routes

- [ ] `GET /lulz LulzController.java`
- [ ] `POST /admin/rofl AdminRoflController.java`

## Mapping / Authorization Decorators

- [ ] `ensure_logged_in`

- [ ] `@login_required` - built in django decorator - only checks for session but not true authorization.
  * Interesting - it papears this decorator is stacked on top of user_passes_test - is that necessary? Is that a pattern we should repeat?
- [ ] `@user_passes_test`
- [ ] `@csrf_exempt` - decorator marks a view as being exempt from the protection ensured by the middleware.

## Mapping / Files

- [ ] /path/to/some/important/file.sh

- [ ] urls.py
- [ ] settings.py
  * This thing seems super insecure - has a secret key, debug is on, simple loggin doesn't have a time, etc. - so let's spend some quality time with this file.

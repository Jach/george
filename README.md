# George

A non-pretentious build server. George isn't your butler, he's just some guy
with a [non-goofy](https://www.youtube.com/watch?v=oo8CrY_ZfFk) name. All he
will do for you is run your custom bash scripts on some schedule and, if you
want, make their outputs available publicly.

# License

George is under the Unlicense and thus released into the public domain.

# Usage

My example deployment: https://george.thejach.com

The below screenshots are from this project.

When you startup the application, it binds to port 27011[^1] and presents an
empty screen with a login button. The initial username and password is
**admin**/**admin**.

Logging in takes you to the main administration page. The main actions to do from this page are to define new jobs to automate, or edit existing ones.
Configuring new jobs is described later, but essentially a job is just a fancy wrapper around a bash script.

You can change various system properties like how many simultaneous jobs you want to allow to run, or what directory root the workspace for jobs should run in,
or change the default username and password.

You can also create some global script templates. These are also just bash scripts, but you can invoke them from any other job as you would a built-in function
or program. You might, for example, have a script to send emails about job successes or failures, or a script that fetches the latest SBCL binary and prepares
it for building and testing a Lisp program[^2].

## Job configuration

Besides creating a bash script to run, you also define a frequency to run the script at. This is done in a cron-like way. (Maybe using clerk.)

Additional options include making the project semi-public from the home page. If you do so for at least one project, then instead of the home page being just a
login form, it will be a login form and a listing of semi-public projects. Visitors can click through to get more public data about those projects. The extent
of information exposed is somewhat customizable. By default, only the name of the job, its current status (passing or failing), a little image that can be
externally embedded for that status, and the date-time when that status was set. You can extend this further by allowing the complete pass/fail history to be
shown,  the script itself to be shown, the script's execution output to be shown, and a whitelist of artifacts kept around from the script's most recent
successful run with custom HTTP headers[^3].

While it can be done in the main script itself, you can also specify separated scripts to run after a success or failure (or both).

As the administrative user, it's as if everything is always public to you.

[^1]: `echo -n "George" | sha256sum | awk '{printf "%d\n", strtonum("0x" substr($1, 1, 8)) % (65534 - 1026 + 1) + 1026}'`
-- fun way to pick a port number in the range 1026-65534 (excluding boundary ports 1025 and 65535).

[^2]: This was the motivation for this project after all, because a certain other CI system in Java kept randomly breaking. I just want to automatically build
and test my handful of Lisp libraries about once a month, man, so I can find out sooner rather than later that an update to SBCL and/or a library I depend on
through quicklisp has broken something.

[^3]: For example (another motivator) if you are generating a code coverage report from tests, and it's in HTML form, but it has some custom JS or CSS to help
make it more readable, you may need to adjust the default CSP header to allow such scripts to execute.

# Contributing

I made this for myself, but if you'd like to add a few more bells and whistles to it, feel free to open an issue or pull request to discuss. Similarly if there
is one feature you wish this had that would make you use it. I'm not necessarily opposed to turning it into more of a full blown CI/CD system, but I don't want
it to become massive. In particular I don't want to plague the universe with yet another dot file declarative schema to pollute projects.

# Project Structure Overview

George is a simple CRUD application with Jingle on the backend and HTMX on the front-end. Sqlite is used for storage. The example is run behind an Apache
server.

The badges for success/fail in jenkins to maybe copy later:
```
$ curl  'https://jenkins.thejach.com/buildStatus/icon?job=cl-ses4&style=plastic'
<?xml version="1.0" encoding="UTF-8"?>
<svg xmlns="http://www.w3.org/2000/svg" width="101.0" height="18">
    <linearGradient id="a" x2="0" y2="100%">
        <stop offset="0" stop-color="#fff" stop-opacity=".7" />
        <stop offset=".1" stop-color="#aaa" stop-opacity=".1" />
        <stop offset=".9" stop-opacity=".3" />
        <stop offset="1" stop-opacity=".5" />
    </linearGradient>
    <rect rx="4" width="101.0" height="18" fill="#555" />
    <rect rx="4" x="47.0" width="54.0" height="18" fill="#e05d44" />
    <path fill="#e05d44" d="M47.0 0h4v18h-4z" />

    <rect rx="4" width="101.0" height="18" fill="url(#a)" />
    <g fill="#fff" text-anchor="middle" font-family="DejaVu Sans,Verdana,Geneva,sans-serif" font-size="11">
        <text x="24.5" y="14" fill="#010101" fill-opacity=".3">build</text>
        <text x="24.5" y="13">build</text>
        <text x="73.0" y="14" fill="#010101" fill-opacity=".3">failing</text>
        <text x="73.0" y="13">failing</text>
    </g>
</svg>
$ curl --head  'https://jenkins.thejach.com/buildStatus/icon?job=cl-ses4&style=plastic'
HTTP/1.1 200 OK
Date: Sat, 01 Mar 2025 03:35:52 GMT
Server: Jetty(12.0.16)
X-Content-Type-Options: nosniff
ETag: /static/23329a7d/buildfailingrednullplastic
Expires: Sun, 01 Jan 1984 00:00:00 GMT
Cache-Control: no-cache, no-store, private
Content-Type: image/svg+xml;charset=utf-8
Content-Length: 1003

$ curl 'https://jenkins.thejach.com/buildStatus/icon?job=cl-catmull-rom-spline&style=plastic'
<?xml version="1.0" encoding="UTF-8"?>
<svg xmlns="http://www.w3.org/2000/svg" width="112.0" height="18">
    <linearGradient id="a" x2="0" y2="100%">
        <stop offset="0" stop-color="#fff" stop-opacity=".7" />
        <stop offset=".1" stop-color="#aaa" stop-opacity=".1" />
        <stop offset=".9" stop-opacity=".3" />
        <stop offset="1" stop-opacity=".5" />
    </linearGradient>
    <rect rx="4" width="112.0" height="18" fill="#555" />
    <rect rx="4" x="47.0" width="65.0" height="18" fill="#44cc11" />
    <path fill="#44cc11" d="M47.0 0h4v18h-4z" />

    <rect rx="4" width="112.0" height="18" fill="url(#a)" />
    <g fill="#fff" text-anchor="middle" font-family="DejaVu Sans,Verdana,Geneva,sans-serif" font-size="11">
        <text x="24.5" y="14" fill="#010101" fill-opacity=".3">build</text>
        <text x="24.5" y="13">build</text>
        <text x="78.5" y="14" fill="#010101" fill-opacity=".3">passing</text>
        <text x="78.5" y="13">passing</text>
    </g>
</svg>
$ curl --head 'https://jenkins.thejach.com/buildStatus/icon?job=cl-catmull-rom-spline&style=plastic'
HTTP/1.1 200 OK
Date: Sat, 01 Mar 2025 03:36:56 GMT
Server: Jetty(12.0.16)
X-Content-Type-Options: nosniff
ETag: /static/23329a7d/buildpassingbrightgreennullplastic
Expires: Sun, 01 Jan 1984 00:00:00 GMT
Cache-Control: no-cache, no-store, private
Content-Type: image/svg+xml;charset=utf-8
Content-Length: 1003
```



# Test a CF installation's HTTPS syslog drain functionality

## What this tests

This script starts 2 Staticfile apps. The sink app is configured as a
user-provided HTTPS syslog drain service, and bound to the generator app. The
syslog drain is configured with a known-unique URL, allowing it to be
positively indentified in the sink's `cf logs --recent` output when received.

The script then makes HTTP requests to the generator app, and checks if any
resulting traffic to the known-unique syslog URL has been generated in the
sink's `cf logs --recent` output.

The tests pass if such traffic is seen in the sink's logs, and fails if it is
not.

It does not currently correlate the volume of logs expected to be seen in the
sink's logs with the volume of requests made towards the generator app.

## Usage

Once this repo is checked out on an OSX (tested), Linux (untested) or Unix
(untested) machine, change into the
`test-basic-https-syslog-drain-functionality` directory.  Log into a
development CF space, and run the following:

```
$ make setup test
```

This will set up some test entities, generate the test traffic, and check the
sink's log output. A pass/fail message will be printed.

To re-test, run:

```
$ make test
```

After you've finished, run the following to clean up the test entities:

```
$ make clean
```

At the end of cleaning up, this will prompt to delete all orphaned routes in
the currently targetted CF space.

## Feedback

Please email any feedback to the `contact+gh` user, where the domain is
`my-github-username.com`.

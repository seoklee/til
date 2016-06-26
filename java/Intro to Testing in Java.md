# Intro to Testing in Java

## Why automate test?

- Detecting the problem first, preferably before they become too expensive to fix
- Repeat the tests in order to achieve ^

Testing

- Achieves stakeholders goals
- Meets functional requirements
- Correctly Handles corner cases

Automating Test

- Deliver Reliability & Quality
- Remove fear of change. You can chagne source code easily

## The Testing Hierarchy

- System Test
	- "End to End" test
	- check that application meets its requirements
	- Slowest, least birttle
	- requires the ability to run the application
- Aggregate Test
	- does a component do its job?
	- eg. Persistence repository can list saved albums
	- slower, less brittle.
- Unit Test
	- Testing just a single unit of funcionality
	- No non-trivial dependencies


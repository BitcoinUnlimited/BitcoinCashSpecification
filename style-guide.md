# Style Guide

This page lays out the rules for contributing to the Bitcoin Cash specification.  This is not intended to be a comprehensive list of rules, please use common sense.

## General Content Guidelines

All contributions should be:

 - As accurate to the current state of Bitcoin Cash as possible
 - As impartial as possible
 - As impersonal as possible

They should not:

- Indicate a preference for or otherwise advocate for the use of a particular feature, configuration, or node implementation
- Call others out by name or imply your personal involvement in making the change

When referring to the "current state of Bitcoin Cash" above, there is not a single interpretation in mind.  One basic litmus test is whether a brand new implementation would need to have a certain change in order to operate without utilizing deprecated/fall-back functionality.  However, if a feature is implemented, and considered functionally complete, by the majority of active node implementations, that may also serve as a sufficient test of currency.

## Node-Specific Content Guidelines

In additional to documenting the "current" state of Bitcoin Cash, this specification also seeks to track partially implemented and experimental features, particularly where it may benefit node developers to avoid stepping on one another's toes.

When it comes to documenting these changes, there is again a range of definitions for what may be considered worth including.  Generally, the set of features that are currently developed and in the <code>master</code> (or similar) branch of a given implementations code base is probably the best place to start.  However, there may be cases where documenting changes earlier than this phase may be useful.  In general, use common sense and try to keep experimental or node-specific content minimal while still including information that may benefit others viewing this specification.

Furthermore, node-specific documentation may be best left to the developers of the implementations.  If you have a change you would like to make about node-specific functionality of an implementation you have not contributed to, at least check the master branch of that implementation's codebase.  When in doubt, reach out to a node developer and ask them to make the change.
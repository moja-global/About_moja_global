# Standard tests

Testing needs to be performed by both developers and maintainers for new or modified code. Developers are expected to perform standard tests before submitting the pull-request, and maintainers also conduct testing when going through the process of assessing a pull-request.
Tests suites should be included in the repositories of existing code, and test suites should be developed for new repositories where tests do not already exist.
The standardised test suites are expected to be completed for all pull-requests. A standardised test suite should include:
1. Unit tests
1. Integration tests
1. System tests

Unit tests are used to test individual components of the code for example a function or procedure. These should be performed by both contributors and maintainers.

Integration tests are used to test the integration of the different units to ensure that the different functions/procedures and modules are interacting correctly. These should also be performed by both contributors and maintainers.

System tests involve testing the complete integrated system, this includes performance tests and guidelines on speed performance to ensure that any new features or bug fixes do not compromise system performance. These tests need to be performed by the maintainer, and the maintainer may also use independent testers is possible. Failure of a system test, or poor performance and may result in non-acceptance, or a request to improve performance before the pull-request if performance is accepted.

Acceptance testing, is the final step in testing and should be conducted for final acceptance of the pull-request. Acceptance testing should be conducted by the 'user' and meet the buisness requirements for the new functionality or bug fix. At present, the community of contributors to moja repositories is relatively small and a detailed framework for acceptance is not required. As the community grows, this will be reviewed and a more hierarchical sturucture may be necessary to ensure that new features fit within the strategic priorities of moja before they are accepted into the Master Branch. 

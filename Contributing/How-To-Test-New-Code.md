## Standard tests

Testing needs to be performed by both developers and maintainers for new or modified code. Developers are expected to perform standard tests before submitting the pull-request, and maintainers also conduct testing when going through the process of assessing a pull-request.

Tests suites should be included in the repositories of existing code, and test suites should be developed for new repositories where tests do not already exist.
The standardised test suites are expected to be completed for all pull-requests. A standardised test suite should include:

1.  Unit tests
2.  Integration tests
3.  System tests

Unit tests are used to test individual components of the code for example a function or procedure. These should be performed by both contributors and maintainers.

Integration tests are used to test the integration of the different units to ensure that the different functions/procedures and modules are interacting correctly. These should also be performed by both contributors and maintainers.

System tests involve testing the complete integrated system, this includes performance tests and guidelines on speed performance to ensure that any new features or bug fixes do not compromise system performance. 

These tests need to be performed by the maintainer, and the maintainer may also use independent testers if possible. Failure of a system test, or poor performance and may result in non-acceptance, or a request to improve performance before the pull-request if performance is accepted.

If you have experience and want to help, please send an email to [tsc@moja.global](mailto:tsc@moja.global). We are looking for test engineers with experience testing C++ code bases or auditing greenhouse gas inventories.

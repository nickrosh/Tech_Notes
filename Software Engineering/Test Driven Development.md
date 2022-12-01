

Test Driven development seeks to minimize the number of errors by flipping the process of development to write tests first, and then write code to pass the tests. The reasoning behind this methodology is that, while it will slow down development, it will lessen tech debt and decrease total working time down the road.

### 3 Laws
1. Write *no* production code except to pass a failing test.
2.  Write only *enough* of a test to demonstrate a failure
3. Write only *enough* production code to pass the test

Thus we fall into a pattern for TDD, called Red Green Refactor

**RED**: Write tests until you fail
**GREEN**: Write code until you pass
**REFACTOR**: Refactor tests and production code. Treat test like you treat production code.

You should strive for 100% code coverage. While you may fall short, it is important to always strive for maximum coverage.
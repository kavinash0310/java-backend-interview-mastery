# Testing Interview Questions

## In which situations would you use PowerMock?

PowerMock is mainly a legacy-code tool when a test must intercept static calls, constructors, or static initialization that cannot yet be isolated through a normal seam. Its custom class loading and bytecode manipulation can make tests brittle and sensitive to framework, test-runner, or JVM changes. For new code, inject collaborators such as a clock, ID generator, or client; if static mocking is still justified, use the scoped API supported by the project's Mockito version where possible and close it after the test. See the [PowerMock project](https://github.com/powermock/powermock) and [Mockito static-mocking API](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/MockedStatic.html).

## How do you verify that a Mockito method was called exactly twice?

Use `verify(mock, times(2))` for an interaction whose count is part of the behavior being tested:

~~~java
verify(eventPublisher, times(2)).publish(any(Event.class));
~~~

If the important contract is the resulting state or emitted events, assert that outcome instead of counting an incidental helper call. Interaction counts are most useful when duplicate sends or a required number of external effects would be a real defect. See the [Mockito verification API](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html).

# quotient
A framework for AI testing.

## Premise

Unit tests are great, E2E tests useful.
But both can become heavy-
we easily get bogged down in the minutia of tests that validate I/O or verify visual effects.
Ultimately users don't care if an API call responds with specifically formatted data.
They care, for example, that they clicked and later their saved datA reappears.
They care, for example, that, if they aren't authorized but should be, that they will be guided back to security.

The first example may be within reach of _typical_ unit/E2E testing;
The second is not- at least not practically. 
That kind of quality assurance is typically the realm of the human. 
But now we have coding agents that can perform which tasks. 

What would such a test look like? 
```
If a user is not logged in and tries to acces a secure feature they should restricted to login with a friendly message.
```
So, while there are several unit tests that can validate the elements of UI and IO within that process...the overreaching test is intractable without more intellect. 

What we need us a framework for running pass/fail, high-level, process type tests.

## Runtime configuration

The runtime configuration should be picked up [from the environment](https://12factor.net/config) by the public interface instance - the HTTP layer in our example.

The entry point has to declare the list of configurations keys that it requires to work. The app throws an exception if any required values are missing since it won't be able to function correctly. Better to refuse to start than to throw an exception in the middle of a user journey.

Only the entry point should look at the environment; this is to ensure we define the required config keys in a single place. Picking up env vars in random places is a sure way to introduce problems that only happen when they really shouldn't (in production).

The same rule applies to the UI instances, whereas they pick up their config from environment variables, but they usually do that at *build time* since they are mostly static.

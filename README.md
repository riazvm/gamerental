# Game Rental Application

## Description

The "Game Rental" application showcases how you can use Axon Framework and Axon Server during software development.
The domain focused on is that of rental services from the perspective of a video game store.

This repository provides just such an application, albeit a demo rather than a full-fledged solution.
It serves the personal purpose of having a stepping stone application to live code during application.
I intend to build upon this sample during consecutive talks, further enhancing its capabilities and implementation as time progresses.
For others, I hope this provides a quick and straightforward look into what it means to build an Axon-based application.

Since Axon is at the basis of the sample, it will incorporate DDD, CQRS, Event Sourcing, and an overall messaging solution to communicate between distinct components.
 
## Project Traversal

Distinct branches will be (made) available per public speaking, sharing a start and final solution branch separately.
Additionally, several branches representing the steps throughout the lifecycle of the "Game Rental" application will be present, allowing you to:
* Check out the exact step that interests you.
* Perform a `git reset --hard step#` to reset your current branch.

Next to providing the convenience of showing the flow, it also serves as a backup during the presentation.

This project currently contains the following steps:

1. The `core-api`, containing the commands, events, queries, and query responses.
2. The `command` model has been created, showing a `Game` aggregate.
3. The application connects to [Axon Cloud](https://console.cloud.axoniq.io/) through the added Axon Server properties to the `application.properties`.
4. The `query` model, a `GameView`, is provided, created/updated, and made queryable through the `GameCatalogProjector`.
5. This step includes the [Reactor Extension](https://github.com/AxonFramework/extension-reactor), which is used by the `GameRentalController`.
6. This step introduces cleaner distributed exceptional handling. It does so by throwing specifics exceptions in `@ExceptionHandler` annotated functions in the `Game` aggregate and `GameCatalogProjector`, containing an `ExceptionStatusCode`.
7. Spring's `@Profile{{profile-name})` annotation has been added to the `Game`, `GameCatalogProjector`, `GameViewRepository` and `GameRentalController`, allowing for application distribution.
8. Preparation to introduce another endpoint based on RSocket, by renaming the controller to contain "rest" in the name and by extracting the exception mapping registration.
9. Introduce the `GameRentalRSocketController`, providing an entry point to the application using [RSocket](https://rsocket.io/).

## Running and testing the application

As this is a Spring Boot application, simply running the `GameRentalApplication` is sufficient.
However, Spring profiles are present, which allow for running portions of this application.
More specifically, there's a `command`, `query`, and `ui` profile present, thus segregating the `Game` aggregate, `GameCatalogProjector`, and `GameRentalController` into their separate runnables.
Furthermore, when you use IntelliJ, you can use the "Run Configurations" from the `./.run` to speed up the startup process.

The application does expect it can make a connection with an Axon Server instance.
Ideally, [Axon Cloud](https://console.cloud.axoniq.io/) is used for this, as is shown in step 3.
If you desire to run Axon Server locally, you can download it [here](http://download.axoniq.io/quickstart/AxonQuickstart.zip).

For validating the application's internals, you can run the tests, use the REST endpoint, or connect with the [RSocket](https://rsocket.io/) endpoint.

When testing through the REST endpoint, [IntelliJ Ultimate](https://www.jetbrains.com/help/idea/http-client-in-product-code-editor.html) you can use the included `.http` files (in the root folder of this project).
The `register-games.http` allows for the registration of several games to build a base catalog.
The `other-requests.http` file contains all other operations for testing.

When testing through RSocket, the most straightforward approach is to install the [RSocket Client CLI](https://github.com/making/rsc), or `rsc` for short.
The README of `rsc` provides concrete explanations on how to install it in your environment.

With `rsc` in place, you can use the following commands to test the application:

* Register a game - `rsc --debug --request --route register --data="{\"gameIdentifier\":\"8668\",\"title\":\"Hades\",\"releaseDate\":\"2020-09-17T00:00:01.000009Z\",\"description\":\"Roguelike dungeon crawler set in ancient Greek mythology\",\"singleplayer\":true,\"multiplayer\":false}" tcp://localhost:7000`
* Rent a game - `rsc --debug --fnf --route rent --data="{\"gameIdentifier\":\"8668\",\"renter\":\"Ben Wilcock\"}" tcp://localhost:7000`
* Return a game - `rsc --debug --fnf --route return --data="{\"gameIdentifier\":\"8668\",\"renter\":\"Ben Wilcock\"}" tcp://localhost:7000`
* Find a game - `rsc --debug --request --route find --data="8668" tcp://localhost:7000`
* Watch the game catalog - `rsc --debug --stream --route catalog tcp://localhost:7000`

## Starting your own Axon project

The [steps](#project-traversal) this project traverses show a common approach towards constructing an Axon application. If you want to begin from scratch, consider these key aspects:

* Use the [AxonIQ Initializr](https://start.axoniq.io/) to kick-start your project.
* Use [Axon Cloud Console](https://console.cloud.axoniq.io/) to connect your application to a context. 
  Using Axon Cloud allows you to persist your events and distributed commands, events, and queries.
* Whenever anything is unclear, check out the [Reference Guide](https://docs.axoniq.io/reference-guide/) or drop a question on the [forum](https://discuss.axoniq.io/).
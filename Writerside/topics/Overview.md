# Overview

This library offers a **type-safe** API to interact with Brawl Stars data, using **validated value classes** that ensure correctness and eliminate raw types. Here’s why you should use it:

1. **Type Safety**: Only validated value classes with predefined constraints and constants are passed around, avoiding raw types and ensuring that the data is always in the correct format. For example, `PlayerTag` is validated before use, ensuring that you won't make mistakes by passing invalid player tags.

2. **Error-Resilient**: The library includes safe and unsafe methods. Safe methods perform checks to avoid invalid data, while unsafe methods give you direct control if you're sure about the input. This flexibility lets you choose the level of safety you need.

3. **Customizable and Extensible**: The library allows you to interact with Brawl Stars' core data, like player details and battle logs, in a clear and efficient manner. Additionally, utility methods (like `Trophies` value class operations) make complex operations easier and more expressive.

4. **Flexible Client Engines**: The library supports different client engines for asynchronous operations, offering flexibility to choose one that best suits your needs. This makes the library suitable for multiple environments, such as JVM or JS.

5. **Comprehensive and Detailed Data**: Whether you need player data, battle logs, or event information, this library gives you access to a wide range of useful and organized data. Operations like comparing trophies or checking player rankings are simplified with utility functions.

6. **Seamless Integration**: With dependencies pre-configured for Kotlin and built on Ktor's client engines, the library easily integrates with existing Kotlin projects. It reduces boilerplate code, allowing you to focus on your core logic.

By using this library, you can handle Brawl Stars data in a more reliable, maintainable, and type-safe manner.
## Description

The Semantic Kernel samples collection is missing a dedicated example for connecting to Azure OpenAI using a custom client configuration. Currently, there is no sample that follows the established Azure OpenAI naming convention for this use case, making it difficult for developers to discover how to use custom client settings when working with Azure OpenAI.

## Expected Behavior

- A new sample should be added under the ChatCompletion examples, specifically for Azure OpenAI with a custom client
- The sample should follow the existing naming convention used by other Azure OpenAI samples in the collection
- The sample should be discoverable alongside other Azure OpenAI examples

## Why This Matters

Developers commonly need to customize their Azure OpenAI client (e.g., to set custom HTTP headers, configure timeouts, or define retry policies). Without a clearly-named, dedicated example following the project's conventions, developers struggle to find the right reference. Adding a properly named sample improves consistency and discoverability within the samples collection.

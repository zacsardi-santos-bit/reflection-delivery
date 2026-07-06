## Description

There are currently no Go sample programs in this repository showing how to use the Vertex AI model evaluation service. Developers building AI applications in Go who want to assess the quality of generative model outputs have nowhere to look for working examples.

## Expected Behavior

The repository should include Go sample code demonstrating three common evaluation scenarios against the Vertex AI evaluation service:

- **ROUGE scoring**: Compare a model's generated text output against a known reference text using a text similarity metric, and retrieve the resulting score.
- **Single-model response evaluation**: Evaluate how well a model's response is grounded in (i.e., supported by) a provided context, and retrieve the score, confidence, and explanation.
- **Pairwise model comparison**: Submit responses from two models along with contextual information and an instruction, and retrieve a judgment indicating which model's response is better along with a confidence and explanation.

All three samples should accept a GCP project ID and a region, write their results to standard output, and return any errors that occur.

## Why This Matters

Teams evaluating LLM outputs in Go currently have no official reference for how to integrate with the Vertex AI evaluation service. Adding these samples lowers the barrier to adoption and helps developers quickly understand how to automate quality assessment of AI-generated content in their own applications.

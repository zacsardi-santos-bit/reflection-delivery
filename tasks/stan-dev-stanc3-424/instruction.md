Implement a modification to the Stan compiler to ensure that variables declared in the 'transformed data' block are correctly promoted to GPU-compatible representations when targeting the OpenCL backend. This will enable GPU acceleration for GLM computations using these variables.

*   Ensure the compiler generates valid C++ output for both the standard and OpenCL backends when a Stan model uses a 'transformed data' block.
*   For the OpenCL backend:
    *   Generate 'matrix_cl<double>' class members for variables declared in the 'transformed data' block that are eligible for GPU acceleration (matrices, vectors, scalars used in GLM functions).
    *   Initialize these OpenCL versions in the model class constructor using 'to_matrix_cl()' on the corresponding CPU variables.
*   Update GLM distribution function calls to use OpenCL-promoted versions of transformed data matrices:
    *   Use the '_opencl__' suffixed variable for GLM functions like 'normal_id_glm_lpdf', 'bernoulli_logit_glm_lpmf', 'poisson_log_glm_lpmf', 'neg_binomial_2_log_glm_lpmf', 'ordered_logistic_glm_lpmf', and 'categorical_logit_glm_lpmf'.
*   For the standard C++ backend, ensure transformed data variables are used directly in GLM function calls without OpenCL promotion.
*   Apply the same OpenCL eligibility logic to transformed data variables as is currently applied to data block variables, expanding GPU promotion to include all variables initialized during data preparation.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.
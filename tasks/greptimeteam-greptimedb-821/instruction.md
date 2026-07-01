Implement the necessary changes to ensure that table creation operations report 0 affected rows, and refactor related internal APIs for improved clarity and functionality. Update the test infrastructure to maintain temporary directories and provide a unified access structure.

*   Update the `create_table` function in `src/datanode/src/sql/create.rs`:
    *   Ensure it returns `Ok(Output::AffectedRows(0))` after successfully creating a table.

*   Modify gRPC-based table creation operations to return 0 affected rows upon success.

*   Implement the `new_distributed` function in `src/frontend/src/instance.rs`:
    *   Signature: `pub(crate) fn new_distributed(dist_instance: DistInstance) -> Self`
    *   This function should construct a frontend `Instance` for distributed mode, marked with `#[cfg(test)]`.

*   Refactor the `alter_expr_to_request` function in `src/common/grpc-expr/src/alter.rs`:
    *   Change the return type to `Result<AlterTableRequest>`.
    *   Return an error if the 'kind' field is missing from the input expression.
    *   Update all callers to remove `Option` unwrapping.

*   Update the `to_table_insert_request` function in `src/common/grpc-expr/src/insert.rs`:
    *   Remove the `schema: SchemaRef` parameter.
    *   Derive column types from the `datatype` field in each column of the request.
    *   Adjust all callers to stop passing a schema argument.

*   Define the `AlterContext` type alias in `src/table/src/table.rs`:
    *   Signature: `pub type AlterContext = anymap::Map<dyn Any + Send + Sync>`
    *   Export this type from the table crate's table module.

*   Modify the `alter` method in the `Table` trait in `src/table/src/table.rs`:
    *   Update the signature to `async fn alter(&self, context: AlterContext, request: AlterTableRequest) -> Result<()>`.
    *   Ensure all implementations (e.g., `MitoTable`, `DistTable`) and call sites are updated to include `AlterContext` as the first argument.

*   Make the `to_flight_data_stream` function in `src/datanode/src/instance/flight.rs` public:
    *   Change the signature to `pub fn to_flight_data_stream(output: Output) -> TonicStream<FlightData)`.

*   Make the `flight` module in `src/datanode/src/instance.rs` public:
    *   Change the declaration to `pub mod flight`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.
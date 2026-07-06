I'm working on the Gleam compiler's JavaScript backend and noticed a bug with how pipeline expressions are compiled.

*   When a pipeline expression is used as a statement in a Gleam function body (i.e., it is not the final return expression), the JavaScript code generator must terminate each intermediate pipeline step with a semicolon.

*   When the echo keyword appears as a non-final step in a pipeline chain (meaning more statements follow in the function body), the generated JavaScript call for echo must end with a semicolon.

*   Given a pipeline followed by another statement that begins with a block expression, the JavaScript output must properly separate each pipeline-derived statement with a semicolon so that JavaScript engines parse them as independent statements rather than as a continued expression.

*   For a pipeline '1 |> echo |> fn(x) { { x + 1 } * 2 }' followed by '{ 1 + 2 } * 3' in a function body, the compiled JavaScript must emit: the echo call terminated by a semicolon, the anonymous function body expression terminated by a semicolon, and the final block expression as the return statement.

*   For a pipeline '1 |> echo' followed by '{ 1 + 2 } * 3' in a function body, the compiled JavaScript must emit: the echo call terminated by a semicolon, and the following block expression as the return statement.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.
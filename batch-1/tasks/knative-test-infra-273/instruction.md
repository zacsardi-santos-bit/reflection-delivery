Implement the following changes to the testgrid integration package to improve its usability and reliability for collecting test results.

*   Export the Filename constant.
    *   Ensure it is a public constant with the value "junit_knative.xml".
*   Update the GetArtifactsDir function.
    *   Return the value of the ARTIFACTS environment variable if it is set and non-empty.
    *   Return "./artifacts" as the default if the ARTIFACTS environment variable is empty or unset.
*   Modify the CreateXMLOutput function.
    *   Accept a TestSuite value and a directory path string as parameters.
    *   Append the XML-encoded TestSuite to the file located at artifactsDir + "/" + Filename.
    *   Ensure the function creates the file if it does not exist.
    *   Ensure the function appends to the file rather than overwriting it.
    *   Ensure calling this function twice with the same directory results in both XML entries being present in the file.
    *   Ensure an empty TestSuite{} serialized by this function produces "<testsuite></testsuite>\n" in the output file.
*   Export the TestSuite struct.
    *   Ensure it is instantiatable as an empty struct literal (TestSuite{}).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.
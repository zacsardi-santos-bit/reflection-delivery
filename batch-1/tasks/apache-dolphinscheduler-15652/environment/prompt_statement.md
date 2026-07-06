I'm working on cleaning up and improving the resources management service in our workflow scheduling platform. There are a few things I'd like to address:

First, some of the service methods have names that don't clearly describe their purpose. The method for uploading a resource file from a multipart form upload and the method for creating a resource file from inline text content should be renamed to better reflect what they actually do. These changes need to be consistent across the service interface, the implementation class, and the REST controller that exposes them.

Second, the resource content update operation has a security gap — it doesn't validate whether the provided file path actually belongs to the tenant's designated storage directory. We should add a check so that if the path falls outside the expected directory, the operation is rejected with an error indicating the path is illegal.

Finally, there are two service methods and their corresponding REST endpoints related to listing which UDF functions are authorized or unauthorized for a specific user. These should be removed entirely from the resources service — both the interface declarations and the implementation.

Could you help me make these changes to the service interface, implementation, and controller?

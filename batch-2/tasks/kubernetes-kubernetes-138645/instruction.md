I'm working on kubeadm and I'd like to change how the container runtime socket (CRI socket) information is sourced when retrieving node registration state.

*   GetNodeRegistration must be updated to accept two directory path parameters: the directory containing kubelet.conf (kubeletConfDir) and the directory containing the kubelet instance configuration file (instanceConfigDir). The old single-file-path signature must be replaced.

*   GetNodeRegistration must read the container runtime socket (CRISocket) from the kubelet instance configuration file located at KubeletInstanceConfigurationFileName within instanceConfigDir, rather than from a node annotation. The value read from the instance config file must be stored in nodeReg.CRISocket.

*   GetNodeRegistration must return an error when the kubelet.conf file is not present in kubeletConfDir.

*   GetNodeRegistration must return an error when the node cannot be found in the cluster via the provided client.

*   getInitConfigurationFromCluster must be updated to accept two directory path parameters (kubeletConfDir string and instanceConfigDir string) before the client parameter, replacing the previous single directory parameter. These must be passed through to GetNodeRegistration when node registration is requested.

*   After getInitConfigurationFromCluster returns successfully with node registration, cfg.NodeRegistration.CRISocket must equal the value read from the instance configuration file ("unix:///foo/bar" when using the testdata/kubelet-instance-config.yaml test fixture).

*   The BuildKubeletArgs input struct must not have a criSocket field. BuildKubeletArgs must not generate a kubelet argument named "container-runtime-endpoint" based on a CRI socket input; this argument is no longer produced by this function.

*   WriteKubeletConfigFiles must return an error when the kubelet instance configuration file (identified by KubeletInstanceConfigurationFileName) is missing from the expected directory.

*   The AnnotateCRISocket function must be removed from the patchnode package. All callers of this function must be updated to no longer call it.

*   A constant KubeletInstanceConfigurationFileName must be defined in cmd/kubeadm/app/constants/constants.go. This constant is the filename of the kubelet instance configuration file used to locate it within the instance config directory.

*   A test data file cmd/kubeadm/app/util/config/testdata/kubelet-instance-config.yaml must exist and contain a valid kubelet configuration with containerRuntimeEndpoint set to "unix:///foo/bar". This file is embedded into tests as configWithContainerRuntimeEndpoint.

*   Node objects no longer need to carry an AnnotationKubeadmCRISocket annotation. Code that previously set or read this annotation to determine the CRI socket must be updated to use the instance configuration file approach instead.


*   Interface details: Type: Function
Name: GetNodeRegistration
Location: cmd/kubeadm/app/util/config/cluster.go
Signature: GetNodeRegistration(kubeletConfDir string, instanceConfigDir string, client clientset.Interface, nodeReg *kubeadmapi.NodeRegistrationOptions) error
Description: Retrieves node registration state from the cluster. Now accepts two separate directory paths: the directory containing kubelet.conf (kubeletConfDir) and the directory containing the kubelet instance configuration file (instanceConfigDir). Populates nodeReg.CRISocket from the instance configuration file in instanceConfigDir rather than from a node annotation.

Type: Function
Name: getInitConfigurationFromCluster
Location: cmd/kubeadm/app/util/config/cluster.go
Signature: getInitConfigurationFromCluster(kubeletConfDir string, instanceConfigDir string, client clientset.Interface, getNodeRegistration bool, getAPIEndpoint bool, getComponentConfigs bool) (*kubeadmapi.InitConfiguration, error)
Description: Internal function that builds an InitConfiguration from cluster state. Now accepts two separate directory paths (kubeletConfDir and instanceConfigDir) instead of one, to pass down to GetNodeRegistration. The returned cfg.NodeRegistration.CRISocket is populated from the instance configuration file.

Type: Constant
Name: KubeletInstanceConfigurationFileName
Location: cmd/kubeadm/app/constants/constants.go
Description: Filename constant for the kubelet instance configuration file. Used to locate the kubelet instance config within the instanceConfigDir directory when reading the container runtime endpoint.

Type: File
Name: kubelet-instance-config.yaml
Location: cmd/kubeadm/app/util/config/testdata/kubelet-instance-config.yaml
Description: Test data file embedded with //go:embed in cluster_test.go as configWithContainerRuntimeEndpoint. Must contain a kubelet configuration with containerRuntimeEndpoint set to "unix:///foo/bar". This value is checked by tests that verify cfg.NodeRegistration.CRISocket after calling GetNodeRegistration and getInitConfigurationFromCluster.

Type: Struct change
Name: BuildKubeletArgs input struct
Location: cmd/kubeadm/app/phases/kubelet/flags.go
Description: The struct used as input to BuildKubeletArgs must not have a criSocket field. The BuildKubeletArgs function must not generate a "container-runtime-endpoint" kubelet argument from the struct; that argument is no longer produced by BuildKubeletArgs.

Type: Function removal
Name: AnnotateCRISocket
Location: cmd/kubeadm/app/phases/patchnode/patchnode.go
Description: The AnnotateCRISocket function must be removed. The patchnode package test file has been deleted entirely, indicating this function is no longer part of the API. Any callers of AnnotateCRISocket must be updated.

Type: Function behavior change
Name: WriteKubeletConfigFiles
Location: cmd/kubeadm/app/phases/upgrade/postupgrade.go
Description: Must return an error when the kubelet instance configuration file (identified by KubeletInstanceConfigurationFileName) is missing. The function signature is unchanged: WriteKubeletConfigFiles(cfg *kubeadmapi.InitConfiguration, kubeletDir string, patchesDir string, ...) error.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.
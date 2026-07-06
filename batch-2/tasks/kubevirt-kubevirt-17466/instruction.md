I'm working on a virtual machine controller in a Kubernetes-based virtualization platform and I need to improve how the running VM instance tracks which version of the VM's configuration it corresponds to.

*   The constant VirtualMachineGenerationAnnotation must be defined in the kubevirt API types (staging/src/kubevirt.io/api/core/v1/types.go) with the string value 'kubevirt.io/vm-generation'. The existing constant DeprecatedVirtualMachineGenerationAnnotation (same value) must be renamed to VirtualMachineGenerationAnnotation.

*   The setGenerationAnnotationOnVmi function must set the 'kubevirt.io/vm-generation' annotation on the VMI with the given generation number formatted as a decimal string. It must merge the annotation into any existing annotations without removing them, and must initialize the annotations map if it is nil.

*   The getGenerationAnnotation function must return a pointer to the string value of the 'kubevirt.io/vm-generation' annotation when it exists on the VMI, and must return (nil, nil) when the annotation is not present or when the annotations map is empty.

*   The patchVmGenerationAnnotationOnVmi controller method must patch the VMI resource via the Kubernetes API to set the 'kubevirt.io/vm-generation' annotation to the generation number formatted as a decimal string. After the patch, retrieving the VMI from the API server must show the annotation with the correct decimal string value.

*   The conditionallyBumpGenerationAnnotationOnVmi controller method must patch the VMI generation annotation to vm.Generation only when the generation differs from the current annotation value AND the VM template spec is identical to the template spec stored in the controller revision (fetched via the Kubernetes API by name). Changes to non-template fields such as RunStrategy must not prevent the bump — only template spec differences prevent it. If the annotation is missing, proceed as if a bump is needed (compare templates and patch if they match).

*   The syncGenerationInfo controller method must set vm.Status.ObservedGeneration and vm.Status.DesiredGeneration. When the VMI generation annotation is a valid integer, ObservedGeneration must equal that integer and DesiredGeneration must equal vm.Generation. When the annotation is missing or malformed (non-integer), the method must patch the VMI with the generation from the controller revision, then set ObservedGeneration = DesiredGeneration = that revision generation value.

*   When the VM controller creates a new VMI, the VMI must include the 'kubevirt.io/vm-generation' annotation set to the VM's current generation as a decimal string. This must apply for all run strategies including Always, Once, and RerunOnFailure.

*   When creating a VMI from a VM template, the resulting VMI annotations must include the 'kubevirt.io/vm-generation' annotation (set to the VM generation as a decimal string) in addition to any annotations from the VM's template spec.

*   The snapshot controller's source spec builder must, when processing an online VM snapshot, copy the current VM's template volumes, domain devices/disks, and dataVolumeTemplates to the snapshot content spec rather than using only the values from the stored VM revision. This ensures that volume migrations (where the destination volume name differs from the original) are correctly captured in the snapshot content.


*   Interface details: Type: Constant
Name: VirtualMachineGenerationAnnotation
Location: staging/src/kubevirt.io/api/core/v1/types.go
Signature: VirtualMachineGenerationAnnotation string = "kubevirt.io/vm-generation"
Description: Constant for the annotation key used to track the VM generation on a VMI. Must be renamed from the existing DeprecatedVirtualMachineGenerationAnnotation constant (same string value "kubevirt.io/vm-generation", just the Go identifier changes).

Type: Function
Name: setGenerationAnnotationOnVmi
Location: pkg/virt-controller/watch/vm/vm.go
Signature: setGenerationAnnotationOnVmi(generation int64, vmi *virtv1.VirtualMachineInstance)
Description: Sets the VirtualMachineGenerationAnnotation ("kubevirt.io/vm-generation") annotation on the VMI with the generation number formatted as a decimal string. Merges with existing annotations without removing them; initializes the annotations map if nil. Package-private function (lowercase name).

Type: Function
Name: getGenerationAnnotation
Location: pkg/virt-controller/watch/vm/vm.go
Signature: getGenerationAnnotation(vmi *virtv1.VirtualMachineInstance) (*string, error)
Description: Returns a pointer to the string value of the VirtualMachineGenerationAnnotation annotation on the VMI if it exists, or nil (with nil error) if the annotation is absent or the annotations map is empty. Package-private function (lowercase name).

Type: Method
Name: patchVmGenerationAnnotationOnVmi
Location: pkg/virt-controller/watch/vm/vm.go
Signature: patchVmGenerationAnnotationOnVmi(generation int64, vmi *virtv1.VirtualMachineInstance) (*virtv1.VirtualMachineInstance, error)
Description: Controller method that patches the VMI resource in the Kubernetes API to set the "kubevirt.io/vm-generation" annotation to the given generation number as a decimal string. Returns the patched VMI and an error. Package-private method (lowercase name).

Type: Method
Name: conditionallyBumpGenerationAnnotationOnVmi
Location: pkg/virt-controller/watch/vm/vm.go
Signature: conditionallyBumpGenerationAnnotationOnVmi(vm *virtv1.VirtualMachine, vmi *virtv1.VirtualMachineInstance) (*virtv1.VirtualMachineInstance, error)
Description: Controller method that bumps the VMI generation annotation to vm.Generation only when: (1) vm.Generation differs from the current annotation value, AND (2) the VM's current template spec is identical to the template spec stored in the controller revision (retrieved via the Kubernetes API, not an indexer). Run strategy changes alone (non-template changes) do not prevent bumping. If the template spec has changed, no patch is issued. If the VMI generation annotation is missing, proceeds as if a bump is needed. Package-private method (lowercase name).

Type: Method
Name: syncGenerationInfo
Location: pkg/virt-controller/watch/vm/vm.go
Signature: syncGenerationInfo(vm *virtv1.VirtualMachine, vmi *virtv1.VirtualMachineInstance, logger *log.FilteredLogger) (*virtv1.VirtualMachineInstance, error)
Description: Controller method that synchronizes VM generation status fields (vm.Status.ObservedGeneration and vm.Status.DesiredGeneration) from the VMI generation annotation. When the annotation is a valid integer: vm.Status.ObservedGeneration = annotation value, vm.Status.DesiredGeneration = vm.Generation. When the annotation is missing or contains a malformed value: patches the VMI annotation with the generation from the corresponding controller revision, then sets vm.Status.ObservedGeneration = vm.Status.DesiredGeneration = revision generation. Package-private method (lowercase name).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.
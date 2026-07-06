I'm working on the Azure IPAM component in Cilium and I need to move subnet tracking from the per-address level up to the per-interface level.

*   A new AzureSubnet struct must be added in pkg/azure/types/types.go with two string fields: ID (the subnet resource ID) and CIDR (the subnet's CIDR range).

*   AzureInterface must gain a Subnet field of type AzureSubnet. This field is the authoritative source of subnet information for the interface.

*   The deprecated AzureInterface.CIDR string field must remain present and be written as a mirror of Subnet.CIDR whenever parseInterface populates Subnet.CIDR.

*   The deprecated AzureAddress.Subnet string field must remain present and be written as a mirror of AzureInterface.Subnet.ID for each secondary address appended by parseInterface.

*   parseInterface must derive subnet information (Subnet.ID, Subnet.CIDR, Gateway) from the first IP configuration that has a subnet reference. This derivation must happen before skipping the primary IP configuration, so that interfaces whose only IP configuration is the primary also have Subnet.ID, Subnet.CIDR, and Gateway populated.

*   The AddressIterator type in pkg/ipam/types/types.go must be a function type with signature func(instanceID, interfaceID, ip string, address Address) error — the poolID string parameter that previously appeared as the fourth argument must be removed.

*   AzureInterface.ForeachAddress must call its AddressIterator callback with the new four-argument signature (instanceID, interfaceID, ip, address) and must no longer pass a poolID argument.

*   azureInterfaceCIDR in pkg/ipam/crd.go must accept an AzureInterface value and return a string: it returns Subnet.CIDR when that field is non-empty, falls back to the deprecated flat CIDR field when Subnet.CIDR is empty, and returns an empty string when neither field is set. When Subnet.CIDR and CIDR disagree, Subnet.CIDR wins.


*   Interface details: Type: Struct
Name: AzureSubnet
Location: pkg/azure/types/types.go
Description: Describes the subnet an Azure network interface is attached to. Azure enforces one subnet per NIC so this is tracked once per interface.
Fields:
  - ID string (json: "id,omitempty") — resource ID of the subnet
  - CIDR string (json: "cidr,omitempty") — CIDR range associated with the subnet

Type: StructField
Name: Subnet
Parent: AzureInterface
Location: pkg/azure/types/types.go
Description: A new field added to AzureInterface of type AzureSubnet, tagged json:"subnet,omitzero".

Type: Type
Name: AddressIterator
Location: pkg/ipam/types/types.go
Description: Function type for the ForeachAddress callback iterator. The poolID string parameter has been removed.
Signature: func(instanceID, interfaceID, ip string, address Address) error

Type: Function
Name: azureInterfaceCIDR
Location: pkg/ipam/crd.go
Signature: azureInterfaceCIDR(iface azureTypes.AzureInterface) string
Description: Internal (unexported) helper that returns the CIDR for an Azure interface. Returns iface.Subnet.CIDR when non-empty; otherwise falls back to the deprecated flat iface.CIDR field; returns empty string when neither field is set. Subnet.CIDR takes precedence when both fields are populated and disagree.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.
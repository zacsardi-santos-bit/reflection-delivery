I'm working on improving bloom filter support in the aya eBPF library.

*   The user-space BloomFilter::contains method in aya/src/maps/bloom_filter.rs must change its signature from accepting a mutable reference (&mut V) to accepting any type that borrows as V (impl Borrow<V>). The method must still return Ok(()) when the element is found and Err(MapError::ElementNotFound) when it is not.

*   A new internal function bpf_map_peek_elem must be added in aya/src/sys/bpf.rs. It must issue a BPF_MAP_LOOKUP_ELEM syscall with no key and with the value pointer set to the provided value. It must return Ok(Some(())) on success, Ok(None) on ENOENT, and Err(e) for other errors. The BloomFilter::contains method must use this function instead of the previous bpf_map_lookup_elem_ptr.

*   The BtfMapDef struct in aya-obj/src/maps.rs must gain a new pub(crate) map_extra: u64 field.

*   The Map enum in aya-obj/src/maps.rs must gain a new pub const fn map_extra(&self) -> u64 method, returning the map_extra value from BtfMapDef for Map::Btf variants and 0 for Map::Legacy variants.

*   The parse_map_info function in aya-obj/src/obj.rs must be updated to return Map::Btf when btf_value_type_id != 0, even if btf_key_type_id == 0. Previously it only returned Map::Btf when btf_key_type_id != 0. The Map::Btf result must also populate the map_extra field from info.map_extra.

*   The parse_btf_map_def function in aya-obj/src/obj.rs must handle the 'map_extra' struct member name, converting it to a u64 and storing it in BtfMapDef.map_extra.

*   The bpf_create_map function in aya/src/sys/bpf.rs must read def.map_extra() and set it in the BPF_MAP_CREATE syscall attribute (map_extra field). When the map type is BPF_MAP_TYPE_BLOOM_FILTER, it must set btf_key_type_id=0 (void key) and still set btf_value_type_id and btf_fd from the BTF map definition.

*   A new BTF-compatible BloomFilter struct must be added in ebpf/aya-ebpf/src/btf_maps/bloom_filter.rs. It must be a #[repr(C)] struct with four generic parameters: a value type T and const usize generics MAX_ENTRIES, FLAGS (default 0), and HASH_FUNCS (default 0). It must provide a const fn new() -> Self constructor, a contains(&self, value: impl Borrow<T>) -> Result<(), c_long> method, and an insert(&self, value: impl Borrow<T>, flags: u64) -> Result<(), c_long> method. It must be exported as BloomFilter from the btf_maps module.


*   Interface details: Type: Function
Name: BloomFilter::contains
Location: aya/src/maps/bloom_filter.rs
Signature: contains(&self, value: impl Borrow<V>, flags: u64) -> Result<(), MapError>
Description: Query whether a value exists in the bloom filter. Changed from the old signature that required &mut V. Uses bpf_map_peek_elem internally.

Type: Function
Name: bpf_map_peek_elem
Location: aya/src/sys/bpf.rs
Signature: bpf_map_peek_elem<V: Pod>(fd: BorrowedFd<'_>, value: &V, flags: u64) -> io::Result<Option<()>>
Description: New pub(crate) function that issues BPF_MAP_LOOKUP_ELEM with no key and the value pointer set to the provided value. Returns Ok(Some(())) on success, Ok(None) on ENOENT, Err(e) for other errors.

Type: Struct
Name: BtfMapDef
Location: aya-obj/src/maps.rs
Description: Must gain a new pub(crate) field: map_extra: u64.

Type: Method
Name: Map::map_extra
Location: aya-obj/src/maps.rs
Signature: pub const fn map_extra(&self) -> u64
Description: New method on the Map enum. Returns m.def.map_extra for Map::Btf variants and 0 for Map::Legacy variants.

Type: Function
Name: parse_map_info
Location: aya-obj/src/obj.rs
Signature: pub const fn parse_map_info(info: bpf_map_info, pinned: PinningType) -> Map
Description: Existing function that must be updated. The condition for returning Map::Btf must change from (info.btf_key_type_id != 0) to (info.btf_key_type_id != 0 || info.btf_value_type_id != 0). Must also populate BtfMapDef.map_extra from info.map_extra.

Type: Function
Name: bpf_create_map
Location: aya/src/sys/bpf.rs
Description: Existing function that must be updated to: (1) set map_extra in the syscall attribute from def.map_extra(); (2) add a special case for BPF_MAP_TYPE_BLOOM_FILTER that sets btf_key_type_id=0 (void key) while still setting btf_value_type_id from the BTF map definition and setting btf_fd from the provided BTF file descriptor.

Type: Struct
Name: BloomFilter
Location: ebpf/aya-ebpf/src/btf_maps/bloom_filter.rs
Description: New #[repr(C)] struct for eBPF kernel-side use. Generic parameters: T (value type), const MAX_ENTRIES: usize, const FLAGS: usize = 0, const HASH_FUNCS: usize = 0. Must provide: const fn new() -> Self, contains(&self, value: impl Borrow<T>) -> Result<(), c_long>, insert(&self, value: impl Borrow<T>, flags: u64) -> Result<(), c_long>. Must be re-exported as BloomFilter from the aya_ebpf::btf_maps module.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.
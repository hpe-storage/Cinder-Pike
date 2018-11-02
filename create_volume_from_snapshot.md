
## Creation of volume from snapshot
---

#### Need for this enhancement:
In HOS8, while creating volume from a snapshot, volume is created independently i.e it is decoupled from snapshot.

V1<br/>
+<br/>
|<br/>
+--- S1<br/>
|<br/>
+<br/>
V2


While creating large number of instances (bootable volumes) from snapshots, timeouts are observed.
This is because volume cannot be resized until background copying task is finished.

#### Solution:
A new extra spec has been added on the volume.<br/>
Name - **hpe3par:convert_to_base**<br/>
Value - True/False; defaults to True<br/>
- True: Volume (from snapshot) is created independently i.e HOS8 behavior
- False: Volume (from snapshot) is created as child of snapshot i.e HOS5 behavior

In below examples, volume-type named "silver" is used.

**Set the extra-spec**:<br/>
`cinder type-key silver set hpe3par:convert_to_base=False`

**Verify**:<br/>
`cinder extra-specs-list`

**Remove the extra-spec**:<br/>
`cinder type-key silver unset hpe3par:convert_to_base`

**Volume creation example**:<br/>
`cinder create --name v1 --volume-type silver 5`<br/>
`cinder snapshot-create --name s1 v1`<br>
`cinder snapshot-list`<br/>
`cinder create --snapshot-id <snap_id> --volume-type silver --name v2 5`<br/>

#### Note regarding size of V2:
If size of V1 and V2 is same, then the proposed solution works perfectly.

However, if size of V2 is greater than size of V1, then volume cannot be grown.
In this case, to avoid any error, V2 is converted to base volume i.e HOS 8 behavior.


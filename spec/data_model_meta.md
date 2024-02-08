# Meta / notes on data model

# Ids
### internal structure / layout
```
    union { 
        uid : u64 
        struct {
            id          : u32
            version     : u16
            <reserved>  : u8
            type        : u8
        }    
    }
```
### comments
```
    Ids used for all internal refs

    Ids have version + type fields

    All data objects have both explicit id + version fields; can be combined <=> uid (u64)

    ids, versions, and typeids are all serial

    unique id (and typeid) always refers to a unique object instance

        eg. id 0x0101, typeid 0x01  => some unique / persistent Feature object

            id 0x0101, typeid 0x01, version 0x02
        and id 0x0101, typeid 0x01, version 0x23    refer to different version(s) of that object

    versions refer to specific versions of that object

    value version=0 = VERSION_LATEST    (alt: VERSION_LATEST = uint.max)

    when making changes:
        
        (1) can create new copy of that value, with new (and incremented!) version id

        or (2) can intentionally overwrite an existing version

        also, (3) can delete versions to prune down version history etc

        [VFUTURE]   add capability to prune down versions, renaming all version identifiers
                    starting serially from 1, skipping holes of versions that are missing or were deleted

    all versions of ANYTHING can add a VersionInfo / VersionRelease annotation
        
        * full object uid (type + version + id)
        * string name, eg. "0.1.1"

        [VFUTURE] needs UI for this

        utility / how to think of this:

            * version ids are akin to git commits, except pruneable / editable
            * named versions (VersionInfos) are akin to git version lables

        "anything" can have version labels, as it makes sense for all of

            Projects, Features, Specs, Examples, and even Impls and Unittests to have them

    how identifier lookup works is fairly important:

        * to get eg. all Specs associated with a given Feature (with id A, version V)

            get all Specs where <Spec>.feature.id == A and .feature.version <= V

            return Specs dedup by .feature.id with latest found version

            (TODO: check that this is accurate)
```
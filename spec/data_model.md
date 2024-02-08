# App Data Model

# Feature   (type = 0x01)
### v1
## Fields
```
    id              : u32
    version         : u16
    name            : str
    descr           : str
    parent_feature  : u64 Feature
    parent_proj     : u64 Project
```
## Associations
```
    child_features  : [u64 Feature]     where Feature.parent_feature == id
    specs           : [u64 Spec]        where Spec.feature           == id
    unittests       : [u64 Unittest]    where Unittest.spec.feature  == id
    impls           : [u64 Unittest]    where Impl.spec.feature      == id
```

# Spec      (type = 0x02)
### v1
## Fields
```
    id              : u32
    version         : u16
    descr           : str
    feature         : u64 Feature
```
## Associations
```
    impls           : [u64 Impl]    where Impl.spec == id
    unittests       : [u64 Impl]    where Unittest.spec == id
```

# Impl  (type = 0x03)
### v1
## Fields
```
    id              : u32
    version         : u16

    spec            : u64 Spec

    lang            : u64 Lang
    module          : u64 Module
    
    imports         : u64 Module | Import

    content         : str

    content_type    : u64 ContentType
    content_name    : str?

    name            : str?
    full_name       : str?
```
# Unittest  (type = 0x04)
### v1
## Fields
```
    id              : u32
    version         : u16
    lang            : u64 Lang
    
    spec            : u64 Spec

    lang            : u64 Lang
    imports         : u64 Module | Import

    body            : str               // unittest body (exprs)
    pre             : str?              // unittest setup (exprs, optional)
    post            : str?              // unittest teardown (exprs, optional)
    decls           : str?              // extra pre-unittest global-level declarations, optional
```

# VersionInfo / VersionRelease / VersionTarget  (type = 0x05)
### v1
## Fields
```
    id              : u64 Any
    name            : str
```

# Lang  (type = 0x06)
### v1
## Fields
```
    id              : u64
    parent_lang     : u64?              // ex "c++" parent of "c++11","c++14"
    name            : str
```
## Assoc
```
    child_langs     : [u64 Lang]    where Lang.parent_lang == id
```

# Module (type = 0x07)
### v1
## Fields
```
    id              : u32
    version         : u16

    name            : str
    path?           : str
```
## Assoc
```
    impls           : [u64 Impl]    where Impl.module == id
```

# Import (type = 0x08)
### v1
## Fields
```
    uid             : u64

    import_module   : u64 Module
    import_symbol   : str
```

# Project (type = 0x09)
### v1
## Fields
```
    id              : u32
    version         : u16

    name            : str
    path            : str?
    repo            : str?
    parent          : u64 Project?
```
## Assoc
```
    child_projects  : [u64 Project]     where Project.parent == id
```

# Example / Program / TestProgram / Target (type = 0x0A)
### v1
## Fields
```
    id              : u32
    version         : u16

    example_of      : u64? Project | Feature | Spec

    name            : str
    target_type     : u64 TargetType
    lang            : u64 Lang
    imports         : u64 Module | Import

    content         : str
```
### vfuture
* add test / profile features + refactor?

# TargetType (type = 0x0B)
### v1
## Enum of
```
    ExampleProgram      = 0x01
    CustomTest          = 0x02      // test : ok iff runs successfully
    ProfileTest         = 0x03      // run (should run ok) + profile result(s)
```
### vfuture
* add test / profile features + refactor?

# ContentType (type = 0x0C)
### v1
## Enum of
```
    GlobalDecl          = 0x01
    ClassMember         = 0x02
    StructMember        = 0x03
    EnumMember          = 0x04
    UnionMember         = 0x05
    NamespacedDecl      = 0x06
```
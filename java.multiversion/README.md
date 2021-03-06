Short sample to show that multiple versions of the same library can be loaded dynamically and used the same time.

Build is managed using **Gradle**.

The solution already defines a gradle wrapper in build.gradle file.

Therefore **gradlew** command can be used.
 
Useful commands for this test:
- **run the app:** gradlew build :versiontest:run
- **clean everything:** gradlew clean

Gradle projects: [each has build.gradle file]
- **v1** - library version 1
- **v2** - library version 2
- **v3** - library version 3
- **v1dep** - dependency of library version 1
- **v2dep** - dependency of library version 2
- **v3dep** - dependency of library version 3
- **versiontest** - application using library version 1, 2, 3 at the same time. Loads v3 from classpath and v1 and v2 from file. 

There is also a `ParentLastClassLoader` that can be used instead in order to load classes first from URL and last from the parent class loader.

**Output**

Version 3 is used directly by being present on the classpath. 
Therefore, by using the root loader as a parent for v1 and v2 URLClassLoader, the classes from v2 (classpath) will be used instead:

- V1 loader :: version = 'core-v3' :: dependency_version = 'core-dep-v3'
- V2 loader :: version = 'core-v3' :: dependency_version = 'core-dep-v3'
- V3 loader :: version = 'core-v3' :: dependency_version = 'core-dep-v3'

In order to fix this v1 and v2 URLClassLoader will need to have null as parent:

- V1 loader :: version = 'core-v1' :: dependency_version = 'core-dep-v1'
- V2 loader :: version = 'core-v2' :: dependency_version = 'core-dep-v2'
- V3 loader :: version = 'core-v3' :: dependency_version = 'core-dep-v3'

By using the `ParentLastClassLoader` this will simply load v1 and v2 from URL and v3 from classpath:

- V1 custom loader :: version = 'core-v1' :: dependency_version = 'core-dep-v1'
- V2 custom loader :: version = 'core-v2' :: dependency_version = 'core-dep-v2'
- V3 custom loader :: version = 'core-v3' :: dependency_version = 'core-dep-v3'

Meaning that the two library versions and their associated dependencies are loaded correctly.

**NOTE:** created using IntelliJ IDEA 2017.1

**NOTE:** `Core` interface could be part of another project which is used by projects `v1, v2 and v3`, which could allow us to cast the newly created instance and work in a typesafe manner. But this might not be doable every time.

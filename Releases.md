# MongoDB Ruby Driver Release Plan

This is a description of a formalized release plan that will take effect
with version 1.3.0.

## Semantic versioning

The most significant difference is that releases will now adhere to the conventions of
[semantic versioning](http://semver.org). In particular, we will strictly abide by the
following release rules:

1. Patch versions of the driver (Z in x.y.Z) will be released only when backward-compatible bug fixes are introduced. A bug fix is defined as an internal change that fixes incorrect behavior.

2. Minor versions (Y in x.Y.z) will be released if new, backward-compatible functionality is introduced to the public API.

3. Major versions (X in X.y.z) will be incremented if any backward-incompatible changes are introduced to the public API.

This policy will clearly indicate to users when an upgrade may affect their code. As a side effect, version numbers will climb more quickly than before.


## Release checklist

### Prerequisites

- Access to the 10gen/ops repo
- Push access to rubygems

Before each relese to Rubygems.org, the following steps will be taken:

1. All driver tests will be run on Linux, OS X, and Windows via continuous integration system.

2. Update the HISTORY wiki page and document all significant commits.

3. Update the version in VERSION and ext/cbson/version.h.

4. Commit: "RELEASE [VERSION]" `git commit -m 'RELEASE [version]'`

5. Tag: `git tag [version]`

6. Copy gem-private_key.pem to repository if it hasn't been copied there already during a previous release (don't worry it's in .gitignore and won't be added to the repository in a commit).

    ```
    cp <10gen/ops>/ruby/gem-private_key.pem <mongodb/mongo-ruby-driver>
    ```

7. Build gems. Ensure that they have the correct versions.

8. Validate the signature

    ```sh
    gem cert --add /path/to/gem-public_cert.pem
    gem install foo-1.0.0.gem -P HighSecurity
    ```

9. Push tags and commits to GitHub (`git push origin master`, `git push --tags`).

10. Build and push docs. (git: mongodb/apidocs) See README in that repo for more info.

11. Push gems to Rubygems.org.

12. Test that the gem is downloadable from Rubygems.org.

13. Close out release in JIRA.

14. Announce release on mongodb-user and mongodb-dev.

## Rake Deploy Tasks
1. rake deploy:change_version[x.x.x]
2. rake deploy:git_prepare
3. rake deploy:git_push
4. rake deploy:gem_build
5. rake deploy:gem_push
# jslib.k6.io

[jslib.k6.io](http://jslib.k6.io), hosted utility libs for k6 scripts.

To make life a little easier during development, make sure to install the following:

- node >= 11.2.0
- yarn >= 1.13.0

## How to add a new JS package

1. Create a new branch from the `main` branch.
2. Create a new lib dir `/lib/{lib_name}`
3. Create a new version dir `/lib/{lib_name}/{desired_version}/`
4. Add an entry file `/lib/{lib_name}/{desired_version}/index.js`
5. Add the lib to `supported.json` (see example below):

```javascript
{
  "{lib_name}": {
    // Available package versions
    "versions": [
      "{desired_version}"
    ],

    // (optional) Documentation's or repository's URL
    "docs-url": "{documentation_or_repository_url}",

    // (optional) As a default, the homepage will point to
    // a package's bundle's index.js. If your package's main
    // bundle name is different; set it here (see the AWS
    // package for instance).
    "bundle-filename": "{index.js}"
}

// Example result
{
  "awesome-lib": {
    "versions": [
      "2.0.3",
      "2.1.2"
    ],
    "docs-url": "https://github.com/example/jslib",
    "bundle-filename": "main.js"
  }
}
```

6. Add test cases in `/tests/basic.js` and `/tests/testSuite.js` to ensure that the added lib is importable and runnable by k6.
7. Update the homepage by running `yarn run generate-homepage`.
8. Verify that the new homepage `/lib/index.html` is legit (Quickly done by running `yarn run verify-homepage`).
9. Create a PR.
10. Get it merged to main (This will push the updates to S3).
11. Browse to https://jslib.k6.io/{lib_name}/{desired_version}/index.js and verify that everything works as expected.

## Add a JS package that requires bundling

Running `yarn run bundle $pkgName@pkgVersion` will try to run webpack bundling on the file located at `/lib/$pkgName/$pkgBundle/index.src.js`.\
If all goes well, you'll get a bundle file `index.js` alongside the "src file".
If there is already a file named `index.js` at the given path, you will be asked to _override or exit_.

When running this command, there will also be an option to run babel transpile on the bundle.

```sh
# Example

yarn run bundle url@1.0.0
```

## How to add a new version of an existing package

1. Create a new branch from the `main` branch.
2. Create a new "version dir" in `/lib/{lib_name}/{desired_version}/`
3. Add entry file for version `/lib/{lib_name}/{desired_version}/index.js`
4. Add the version to `supported.json` like:

```javascript
{
  "my-lib": [
    "1.0.2",
    // Use semantic versioning
    "{desired_version}"
  ]
}

// Example result
{
  "my-lib": [
    "1.0.2",
    "2.0.3"
  ]
}
```

5. Add test cases in `/tests/basic.js` to ensure that the ported lib is importable and runnable by k6.
6. Update the homepage by running `yarn run generate-homepage`.
7. Verify that the new homepage `/lib/index.html` is legit (Quickly done by running `yarn run verify-homepage`).
8. Create a PR.
9. Get it merged to the main branch (This will push the updates to S3).
10. Browse to https://jslib.k6.io/{lib_name}/{desired_version}/index.js to verify that everything works as expected.

## Updating styling and layout of the "Homepage."

The homepage of https://jslib.k6.io live in the `/static` dir.\
The final `index.html` page gets generated by running `yarn run generate-homepage`.

Useful dev commands:

```bash
# Listen to file changes, restart dev server and serve index.html
yarn run develop-homepage

# Quickly serve index.html
yarn run verify-homepage

# Generate index.html
yarn run generate-homepage
```

## Installing new package via npm and automagically adding it to libs

The outcome of this might vary depending on the quality of the desired node_module you want to add.

The following command will try to:

1. Install the desired {package_name}
2. Copy the module to `/lib`
3. Update the homepage `/lib/index.html`

```bash
yarn run add-pkg {package_name}
```

Once run successfully, make sure that you do the following:

- Add test case in `/tests/basic.js` to ensure that the ported lib is importable and runnable by k6.
- Verify that the new homepage `/lib/index.html` is legit.

## Updating a version of a JS package listed in _package.json dependencies_

Some of the JS packages in `/lib/` can be libraries that exist on NPM today.
The following steps will help you add a specific (or latest) version of an existing npm package:

1. `yarn upgrade {my_package}@version` (for specific version) `yarn upgrade {my_package} --latest` (for the latest).
2. `yarn run copy-libs`

If all went well, the following should have happened:

- New version dir and entry file created `/lib/{my_package}/{new_version}/index.js`.
- New version entry added to `support.json` (see example below)

```javascript
{
  "my_package": [
    "1.0.2", // Previous version
    "2.0.0" // New version added by copy-libs command.
  ]
}
```

If not, you will have to manually create the new version as described [here](#how-to-add-a-new-version-of-an-existing-package).

Once a new version dir has been created and added to `supported.json`, it is time to:

3. Add a test case in `/tests/basic.js` to ensure that the added version is importable and runnable by k6.
4. Update the homepage by running `yarn run generate-homepage`.
5. Verify that the new homepage `/lib/index.html` is legit (Quickly done by running `yarn run verify-homepage`).
6. Create a PR and get it approved. 
7. Merge main (This will push the updates to S3).
8. Browse to https://jslib.k6.io/{lib_name}/{desired_version}/index.js

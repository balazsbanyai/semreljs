# Semantic release usage demo with an android project

To set up a project for semantic release versioning using the [official tool](https://github.com/semantic-release/semantic-release/tree/master):
- Add [package.json](https://github.com/balazsbanyai/semreljs/blob/main/package.json)
This contains the details that the tool will pick up.
- Configure the tool using the package json. Below is a basic config that serves our purpose. For additional configuration options see [docs](https://semantic-release.gitbook.io/semantic-release/usage/configuration#configuration-file)
  ```package.js (excerpt)
  // ...
  "release": {
    "branches": ["main"], // the branches which should be considered "release" branches 
    "plugins": [ 
      "@semantic-release/commit-analyzer",
      "@semantic-release/release-notes-generator",
      "@semantic-release/github", // will create github release
      [ // this is a definition of a custom plugin
        "@semantic-release/exec", // it will hook into the "exec" phase
        {
          // It will run the command specified here. In this example project,
          // I write the version for debugging to a prop file, but in 
          // general, running the gradle publish command here is the best approach,
          // because in case of a failed build, it will interrupt the release process. 
          "publishCmd": "./gradlew publish -Pversion=${nextRelease.version}" 
        }
      ]
    ]
  }
  // ...
  ```
- CI configuration (example for github actions)
  - Many CI systems do a _shallow_ checkout, meaning that the history is not available. This will prevent the tool from properly functioning, so this must be disabled. With github actions `fetch-depth: 0` does this.
    ```
    - uses: actions/checkout@v2
      with:
          fetch-depth: 0
    ```
  - Install node
    ```
    - uses: actions/setup-node@v4
      with:
        node-version: '20.x'
    ```
  - Install semantic release globally
    ```
    - name: "install semantic release"
      run: npm install -g semantic-release
    ```
  - Install the exec plugin for semantic release
    ```
    - name: "install semantic release exec plugin"
      run: npm install -g @semantic-release/exec
    ```
  - Run semantic release (this will run whatever we configured in the package.json)
    ```
    - name: "run semrel"
      run: npx semantic-release
    ```

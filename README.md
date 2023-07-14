# Shopware Workspace

## How to use?

Copy the .devcontainer folder in to your plugins root directory and replace `<PLUGIN-NAME>` in those files with your plugin name (so it gets correctly mapped).
The structure should look like this:

```
# structure of a plugin-based theme
├── .devcontainer
│   ├── .devcontainer.json
│   ├── Dockerfile
│   └── prebuild
│       └── .devcontainer.json
├── composer.json
├── README.md
└── src
    ├── ...
    └── YourPluginName.php
```

You can then create a codespace from your repository and choose either the pre-build image or let codespace build it for you.

![Dev container selection screen](screenshots/Screenshot%202023-07-14%20at%204.47.39 PM.png)
# Zed

[Zed](https://zed.dev) is a minimal code editor crafted for speed and collaboration with humans and AI.

## Agent Servers

[Agent Client Protocols (ACPs)](https://agentclientprotocol.com/get-started/introduction) can be installed from a registry.
However, the registry-installed ACPs may clash with the ACPs installed on the system.
It is possible to configure ACPs to use a specific version of an ACP installed on the system
by configuring the `agent_servers` in the `settings.json` file.

```json title="settings.json"
"agent_servers": {
  "opencode": {
    "default_config_options": {
      "model": "cscs/zai-org/GLM-5.2"
    },
    "type": "custom",
    "command": "<PATH_TO>/.opencode/bin/opencode",
    "args": ["acp"]
  },
  "claude-acp": {
    "type": "registry"
  }
}
```

??? warning "OpenCode ACP installed from registry error"
    ```
    Server exited with status exit status: 1
    Error: Unexpected error
    Database is not empty and has no session table
    ```

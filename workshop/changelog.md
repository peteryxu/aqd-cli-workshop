## Tracking changes with this workshop

16th September 2025

- update workshop to include Q CLI v1.15.0
- update the experimental section in advanced setup
- update the command section to add some additional warning around context window usage when running commands
- added details around how to enable/disable auto compaction of context, and cleaned up the /compact section
- update custom agent create to include generation mode
- updated custom agent to include the prompt in the schema
- updated custom agent hooks to include an example using agentSpawn
- added new section to cover tangent mode (experimental)
- added new section to cover todos (experimental)

31st August 2025

- merge PR from community to address two issues (#2 and #3)

28th August 2025

- added a Rules section and update custom agent to follow rules nomlecature
- added new resource - [Mastering Amazon Q Developer with Rules](https://aws.amazon.com/blogs/devops/mastering-amazon-q-developer-with-rules/?trk=fd6bb27a-13b0-4286-8269-c7b1cfaa29f0&sc_channel=el)
- update and change from "steering" to "rules"

23rd August 2025

- update MCP section to incorporate new settings in v1.14.1 to disable MCP Servers from custom agents
- update custom agent section to incorporate new /agent swap option

11th August 2025

This is a big refactor as there was significant changes to how Amazon Q CLI works. /hooks and /profiles are now gone, MCP Server configuration has changed.

- updated to reflect changes in Q CLI v1.13.1 and v1.13.2 - **big update**
- deprecated /profile to /agent and updated content to reflect this change, including changes to context and how this is managed (significant change, removal of global context)
- refactored tasks, including removing and adding based on updated Amazon Q CLI functionality
- added a new image task
- added new section on fine grain tool permissions



22nd July 2025

- updated login section
- added a new tip on how to record your q sessions
- added a new tip on setting alerts to track task completion


21st July 2025

- updated based on version Q CLI v1.12.6
- added new settings to setup section
- added new link to /knowledge
- reference to SigV4 experimental


7th July 2025

- updates based on Amazon Q CLI v1.12.3
- update workshop references to /context hooks to /hooks
- update /load and /save changes
- add info on beta settings

23rd June 2025

- update MCP to show how you can disable MCP Servers using the disable feature

19th June 2025

- updated MCP section to address --arg now added to the mcp add command

17th June 2025

- split out the main getting started lab into two sections, core and advanced
- added a new section on prompt good practices
- split out the setup section into basic and advanced
- updated to include new features in Amazon Q CLI (subscribe and model)

19th May 2025

- added "/context show -expand" to show status of context hooks
- added "/prompts" section with new tasks around configuring your own MCP Server
- updated somem confusion around what "global" context is

17th May 2025

- incorporate feedback from GP (address typos, make clearer a few sections, add additional use case)

16th May 2025

- updated workshop for Amazon Q CLI 1.10
- add new section on managing conversations that includes --resume, /save and /load which were added in 1.10
- added additional headings to cover topics to make it easier to find topics of interest
- add additional info on q mcp command which allows you to manage your mcp.json file
- fix incorrect mcp.json settings
- fix missing q update command
- updated MCP section to include workspace vs global settings

15th May 2025

- Initial version of this workshop


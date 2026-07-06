I work at a company that uses single sign-on for DataHub, so I can't do username/password auth or generate a personal access token myself. Right now the CLI config command only supports those two paths (passing a token directly or username and password), which means I have to open the web UI, make a token there, and paste it back into my config, which is clunky.

What I want is a browser-based SSO login mode on the configuration command. When I use it, it should open a browser window, let me finish my org's identity provider login, and then automatically generate a token and save it along with the host URL to my config file. Print me a confirmation message so I know it worked, and specifically save the generated token name plus the host. This SSO mode has to be mutually exclusive with directly supplying a token or username/password, so if I combine them it should fail with a clear error.

Also there's a companion flag I need for support-access scenarios, oh and it only makes sense alongside SSO mode, so combining it with SSO signals a special access request but using it on its own (without SSO enabled) should be rejected with a clear error message.

The default token lifetime should be sensible depending on the deployment: shorter for cloud-hosted instances, longer for local dev instances. For cloud URLs that carry a path suffix identifying the backend service, derive the frontend URL by stripping that suffix. For local instances, translate the backend port to the frontend port automatically.

And one more thing, if there are already CLI-generated tokens active for that user, print a warning with a link to the token management page so I know to go clean up stale tokens. This all lives in the CLI configuration command code.

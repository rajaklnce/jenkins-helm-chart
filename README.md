 # Jenkins App using  Kubernetes with Docker Container

## Tasks

1. Goal is to create a docker based Jenkins application (app-A). Push the code in this app to a GitHub repo and share the URL
2. This application (app-A) has to be Jenkins based. It watches an external public GitHub repository which is a node or java app (app-B) which can be built using a ‘npm install’ or ‘mvn clean install’. The watching of this external app (app-B) is by using web-hooks
3. Whenever a PR happens to this external repo, it triggers build and test steps. On completion of build and test steps, the Jenkins app (app-A) has to post a comment on the PR indicating success. If the build/test fails then the comment has to denote failure


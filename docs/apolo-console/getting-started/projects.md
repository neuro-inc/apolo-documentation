# Projects

Projects are units of collaboration on a platform.

Each Apolo platform artifact e.g. job, storage file, image, app instance, etc. belongs to some project.

Users could invite other users into their project specifying access level (reader / writer / manager / admin).

Refer to `apolo admin` for project administration and to `apolo config switch-project` to, well, switch between projects.

Most of Apolo CLI accept `--project` flag to perform various commands in corresponding projects.

### Project roles

Project contributors could have one of available roles: reader, writer, manager or admin. Each subsequent role expands on the previous one.

#### Reader

Readers are only able to read project artifacts: download / read storage, blobs, check job statuses and logs, access workloads via HTTP endpoints, pull images, etc.

#### Writer

User with this role is able to perform updates of artifacts -- write new files or update existing in storage or blobs, run new workloads or terminate existing, push images, etc.

#### Manager

Project managers could also invite other users into projects, promote/demote their roles up to manager access level.

#### Admin

Administrators are able to remove project, promote/demote administrators.

When you create project, you become it's administrator.

# Swift Docker Development Template

Get started developing Swift 3 on any platform supporting Docker.
Using this you can easily develop server side Swift even on a Windows computer.

## Requirements
- docker
- docker-compose

## Initialize a basic project scaffold:
```
docker-compose run app swift package init --type executable
```
SPM will generate a project from within docker container targeted the mounted volume.
These files can now be accessed and edited from the host system.

## Run your project
```
docker-compose up
```

## Example
Say we want to try out [Vapor](http://vapor.codes), the web framework for Swift.

First initialize a new SPM project, and confirm it all works with ```docker-compose up```
### Package.swift
Update your package file to look like this:
```
import PackageDescription

let package = Package(
    name: "app",
    dependencies: [
        .Package(url: "https://github.com/vapor/vapor.git", majorVersion: 1, minor: 5)
    ]
)
```

Update main.swift
```
import Vapor

let drop = Droplet()

drop.get("/hello") { _ in
  return "Hello Vapor"
}

drop.run()
```

By default Vapor runs on port 8080, so we will expose that to our host.
```
#docker-compose.yml
version: '2'
services: 
  app:
    build: .
    command: bash -c "swift build && ./.build/debug/app"
    volumes:
      - .:/app
    ports:
      - "8080:8080"
```

Now run
```docker-compose up```

You should see a bunch of output from the container while it fetches and compiles all dependencies.
It now boots up the server, and by visiting ```localhost:8080/hello``` you should see activity in your console, and you will be greeted in your web browser.

From here on, start hacking in your favorite editor!
Refer to docs at [vapor.codes](https://vapor.github.io/documentation/).

## Production
TBD
You probably want to build a different container with all the dependencies already isolated inside. At least build for production instead of debug. ```swift build --configuration release```
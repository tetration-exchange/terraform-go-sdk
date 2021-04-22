# tetration-go

Golang SDK for working with a Cisco Tetration installation using the [Cisco Tetration API](https://www.cisco.com/c/en/us/td/docs/security/workload_security/tetration-analytics/sw/config/b_Tetration_OpenAPI.html).

## Usage

This project uses [Go modules](https://github.com/golang/go/wiki/Modules) for dependency management, and can be added to a new code base by running the following command from within another Go project:

```
go get gitlab.com/ignw1/internal/tetration/tetration-go
```

Once added the calling program needs to parse API credentials and URLs for a Tetration API endpoint, and then the SDK can be instantiated and used to make calls against a Tetration API endpoint using this configuration:

```golang
package main

import (
    "fmt"
    "os"

    "gitlab.com/ignw1/internal/tetration/tetration-go"
)

var (
    APIURL              = os.Getenv("TETRATION_API_URL")
    APIKey              = os.Getenv("TETRATION_API_KEY")
    APISecret           = os.Getenv("TETRATION_API_SECRET")
    defaultClientConfig = tetration.Config{
        APIKey:                 APIKey,
        APISecret:              APISecret,
        APIURL:                 APIURL,
        DisableTLSVerification: false,
    }
)

func main() {
    client, err := tetration.New(defaultClientConfig)
    if err != nil {
        panic(err)
    }
    scopes, err = client.ListScopes()
    if err != nil {
        panic(err)
    }
    fmt.Printf("Listed scopes: %+v\n",scopes)
}
```

If the user does not have permission to make a call, an error will be returned and logged.

## Development

### Environment Setup

#### Gitlab

1. Make a [Gitlab personal access token](https://docs.gitlab.com/ee/user/profile/personal_access_tokens.html#creating-a-personal-access-token)
2. Create a `.netrc` file in your home directory:

```bash
# contents of ~/.netrc:

machine gitlab.com
login <your_gitlab_username>
password <your_personal_access_token>
```

### Testing

Unit tests can be run via

```bash
make test-unit
```

In order for a test file to be executed ONLY as part of the unit test run, add a build tag to the top of the file (including blank line):

```golang
// +build all unittests

```

Integration tests attempt to run test cases against an actual Tetration API endpoint, using the configuration in the project [variables file](./.env). This file is git ignored so won't be pushed up locally and thus each developer working on the code base can locally and privately use their choice of individual API resource and credentials.

Ensure the following values have been updated in the [variables file](./.env).

```bash
# API Endpoint for a Tetration API server
TETRATION_API_URL=https://ignwpov.tetrationpreview.com
# API public key for authentication and authorization requests to a Tetration API endpoint
TETRATION_API_KEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
# API private key for authentication and authorization requests to a Tetration API endpoint
TETRATION_API_SECRET=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
# Id for the scope all scopes created as part of integration tests
# will be rooted under
TETRATION_ROOT_SCOPE_APP_ID=5ceea87b497d4f753baf85bc
# Name of the scope all tags created as part of integration tests
# will be rooted under
TETRATION_ROOT_SCOPE_APP_NAME=Ignwpov
# Id for the app scope that all application resources created as part of integration tests will be rooted under
TETRATION_APP_SCOPE_ID=5ce71503497d4f2c23af85b7
```

Integration tests can be run via

```bash
make test-integration
```

Integration tests

In order for a test file to be executed ONLY as part of the integration test run, add a build tag to the top of the file (including blank line):

```golang
// +build all integrationtests

```

Run all tests via

```bash
make test
```

To run a single test (or test names which match a regex pattern), replace `TEST_NAME` in the Makefile with the name or pattern and run via

```bash
make it
```

### Publishing

Follow [semantic versioning](https://semver.org) when releasing new versions of this library.

Releasing involves tagging a commit in this repository, and pushing the tag. Tagging and releasing of new versions should only be done from the master branch after an approved Pull Request has been merged.

To publish a new version, run

```bash
git tag vX.Y.Z
git push origin vX.Y.Z
```

To consume published updates from other repositories that depends on this module run

```bash
go get gitlab.com/ignw1/internal/tetration/tetration-go@vX.Y.Z
```

and the go `get` tool will fetch the published artifact and update that modules `go.mod` and`go.sum` files with the updated dependency.

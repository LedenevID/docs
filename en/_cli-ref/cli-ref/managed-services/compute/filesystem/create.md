# yc compute filesystem create

Create a filesystem

#### Command Usage

Syntax: 

`yc compute filesystem create <FILESYSTEM-NAME> [Flags...] [Global Flags...]`

#### Flags

| Flag | Description |
|----|----|
|`--name`|<b>`string`</b><br/> A name of the filesystem.|
|`--description`|<b>`string`</b><br/> Specifies a textual description of the filesystem.|
|`--labels`|<b>`key=value[,key=value...]`</b><br/> A list of label KEY=VALUE pairs to add.|
|`--zone`|<b>`string`</b><br/> The zone of the filesystem to create.|
|`--type`|<b>`string`</b><br/> Specifies the type of the filesystem to create.|
|`--size`|<b>`byteSize`</b><br/> Specifies the size of the filesystem in bytes. You can also use K and M suffixes.|
|`--block-size`|<b>`byteSize`</b><br/> Specifies the block size of the filesystem in bytes. You can also use K and M suffixes.|
|`--async`| Display information about the operation in progress, without waiting for the operation to complete.|

#### Global Flags

| Flag | Description |
|----|----|
|`--profile`|<b>`string`</b><br/>Set the custom configuration file.|
|`--debug`|Debug logging.|
|`--debug-grpc`|Debug gRPC logging. Very verbose, used for debugging connection problems.|
|`--no-user-output`|Disable printing user intended output to stderr.|
|`--retry`|<b>`int`</b><br/>Enable gRPC retries. By default, retries are enabled with maximum 5 attempts. Pass 0 to disable retries. Pass any negative value for infinite retries. Even infinite retries are capped with 2 minutes timeout.|
|`--cloud-id`|<b>`string`</b><br/>Set the ID of the cloud to use.|
|`--folder-id`|<b>`string`</b><br/>Set the ID of the folder to use.|
|`--folder-name`|<b>`string`</b><br/>Set the name of the folder to use (will be resolved to id).|
|`--token`|<b>`string`</b><br/>Set the OAuth token to use.|
|`--format`|<b>`string`</b><br/>Set the output format: text (default), yaml, json, json-rest.|
|`-h`,`--help`|Display help for the command.|
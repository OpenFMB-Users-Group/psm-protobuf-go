# Go language protobuf for OpenFMB operational use cases

This repository contains the [Go](https://golang.org/) programming language Protocol Buffer (protobuf) definitions based on the OpenFMB operational use case data model located [here](https://gitlab.com/openfmb/data-models/ops).

## Including in your project

There are a couple of methods for adding these definitions to your project.

### Using Go Modules

Add the following to the go.mod file in your project:

```go
require (
    gitlab.com/openfmb/psm/ops/protobuf/go-openfmb-ops-protobuf <release-tag-label>
)
```

### Dep

NOTE: The "dep" method is set to be deprecated in the near future and should only be used if your project currently uses "dep".

## Using

After adding the depencency in your project, you are ready to include the protobuf definitions into your source files like this:

```go
import (
    commonmodule "gitlab.com/mthollylab/go-openfmb-protobuf/openfmb/commonmodule"
    metermodule "gitlab.com/mthollylab/go-openfmb-protobuf/openfmb/metermodule"
)
```

NOTE: You should always import "commonmodule" as every other module (i.e. "metermodule") depends on definitions inside of common.

After importing the crate, you can now start using the protobuf definitions like this:

```go
func main() {
    // Create a new MeterReadingProfile message
    mrp := &metermodule.MeterReadingProfile{};

    // Set the time quality for this message
    tq := &commonmodule.TimeQuality{
        ClockFailure:         false,
        ClockNotSynchronized: false,
        LeapSecondsKnown:     true,
        TimeAccuracy:         commonmodule.TimeAccuracyKind_TimeAccuracyKind_unspecified,
    }

    // Create the TimeStamp
    t := time.Now()
    ts := &commonmodule.Timestamp{
        Seconds:  uint64(t.Unix()),
        Fraction: uint32(t.Nanosecond() * (1/2 ^ 32)),
        Tq:       tq,
    }

    // Create the IdentifiedObject for this mesage
    mi_id := &commonmodule.IdentifiedObject{}
    mi_id.MRID := uuid.NewV4()

    // Create the MessageInfo
    mi := &commonmodule.MessageInfo{}
    mi.IdentifiedObject = mi_id
    mi.MessageTimeStamp = getCurrentDateTime()

    // Create the ReadingMessageInfo
    rmi := &commonmodule.ReadingMessageInfo{}
    rmi.MessageInfo = mi

    // Set the ReadingMessageInfo for the profile
    mrp.ReadingMessageInfo = rmi
    
    //
    // Continue populating the message
    //

    // Encode the message into the protobuf byte format
    data, _ := proto.Marshal(mrp)

    // Do what you want with the bytes now
}
```

## Copyright

See the COPYRIGHT file for copyright information of information contained in this repository.

## License

Unless otherwise noted, all files in this repository are distributed under the Apache Version 2.0 license found in the LICENSE file.

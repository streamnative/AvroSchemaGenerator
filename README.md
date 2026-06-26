# StreamNative.AvroSchemaGenerator
Generate Apache Avro schemas from .NET types, with support for **RECURSIVE SCHEMA**.

This package is a StreamNative-maintained distribution based on [Sharp-Pulsar/AvroSchemaGenerator](https://github.com/Sharp-Pulsar/AvroSchemaGenerator). The original project is licensed under the Apache License, Version 2.0.

## Getting Started
Install the NuGet package [StreamNative.AvroSchemaGenerator](https://www.nuget.org/packages/StreamNative.AvroSchemaGenerator/):

```bash
dotnet add package StreamNative.AvroSchemaGenerator
```

Then copy/paste the code below:

```csharp
using AvroSchemaGenerator;
public class Course
{
    public string Level { get; set; }
        
    public int Year { get; set; }
        
    public string State { get; set; }
        
    public string Gender { get; set; }
}
var avroSchema = typeof(Course).GetSchema();
```

By default, `StreamNative.AvroSchemaGenerator` generates schema with optional fields. The code below is an example of how to mark fields as required.

```csharp
using System.ComponentModel;
using System.ComponentModel.DataAnnotations;
using AvroSchemaGenerator;
public class Course
{
    [Required]
    public string Level { get; set; }

    [Required]
    public int Year { get; set; }

    [Required]
    public string State { get; set; }
        
    [Required]
    public string Gender { get; set; }
}
var avroSchema = typeof(Course).GetSchema();
```

You can assign a default value as well
```csharp
using System.ComponentModel;
using System.ComponentModel.DataAnnotations;
using AvroSchemaGenerator;
public class Course
{
    [DefaultValue("200")]
    [Required]
    public string Level { get; set; }

    [Required]
    public int Year { get; set; }

    [DefaultValue("Closed")]
    public string State { get; set; }

    public string Gender { get; set; }
}
var avroSchema = typeof(Course).GetSchema();
```
## Aliases
```csharp
[Aliases("OldCourse")]
public class Course
{
    [Aliases("Level")]
    public string NewLevel { get; set; }
}
```
## Logical Types
```csharp
public class MessageTimeKind
{
    [LogicalType(LogicalTypeKind.TimeMicrosecond)]
    public TimeSpan TimeMicros { get; set; }

    [LogicalType(LogicalTypeKind.TimeMillisecond)]
    public TimeSpan TimeMillis { get; set; }
}
	
public class MessageTimestampKind
{
    [LogicalType(LogicalTypeKind.TimestampMicrosecond)]
    public DateTime StampMicros { get; set; }

    [LogicalType(LogicalTypeKind.TimestampMillisecond)]
    public DateTime StampMillis { get; set; }
}
	
public class MessageDateKind
{
    [LogicalType(LogicalTypeKind.Date)]
    public DateTime CreatedTime { get; set; }
        
    public AvroDecimal Size { get; set; }
        
	public string DayOfWeek { get; set; }
}
```

## Custom Avro Definition
```csharp
public class CustomDefinition
{
    [AvroSchema("{\n" +
                "  \"type\": \"bytes\",\n" +
                "  \"logicalType\": \"decimal\",\n" +
                "  \"precision\": 10,\n" +
                "  \"scale\": 6\n" +
                "}")]
    public AvroDecimal DecimalAvro { get; set; }
}
```

## NOTE
- Don't use same declaring type as dictionary value
- Don't use same declaring type as list argument
- Dictionary key must be a string type

## License

This project is licensed under the Apache License Version 2.0 - see the [LICENSE](LICENSE) file for details.

## Attribution

This project is based on [Sharp-Pulsar/AvroSchemaGenerator](https://github.com/Sharp-Pulsar/AvroSchemaGenerator), which is licensed under the Apache License, Version 2.0. StreamNative maintains this package as a separate distribution with StreamNative-specific packaging and maintenance changes. See [NOTICE](NOTICE) for details.

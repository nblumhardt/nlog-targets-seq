# NLog.Targets.Seq [![NuGet Pre Release](https://img.shields.io/nuget/vpre/NLog.Targets.Seq.svg)](https://nuget.org/packages/NLog.Targets.Seq) [![Build status](https://ci.appveyor.com/api/projects/status/o22e6dq0mkftaggc?svg=true)](https://ci.appveyor.com/project/datalust/nlog-targets-seq)  [![Join the chat at https://gitter.im/datalust/seq](https://img.shields.io/gitter/room/datalust/seq.svg)](https://gitter.im/datalust/seq)

An NLog target that writes events to [Seq](https://getseq.net). The target takes full advantage of the structured logging support in **NLog 4.5** to provide hassle-free filtering, searching and analysis.

Projects using earlier NLog versions require the [_Seq.Client.NLog_ package](https://nuget.org/packages/seq.client.nlog) instead.

### Getting started

After installing NLog, install the _NLog.Targets.Seq_ package from NuGet:

```
Install-Package NLog.Targets.Seq -Pre
```

Then, add the target and rules entries to your NLog configuration:

```xml
  <targets async="true">
    <target name="seq" xsi:type="Seq" serverUrl="https://localhost:5341" apiKey="" />
  </targets>
  <rules>
    <logger name="*" minlevel="Info" writeTo="seq" />
  </rules>
```

Set the `serverUrl` value to the address of your Seq server, and provide an API key if you have one set up.

A complete sample application and _NLog.config_ file can be found [here in this repository](https://github.com/datalust/nlog-targets-seq/tree/dev/sample/Example).

### Structured logging with NLog

NLog 4.5 adds support for [message templates](https://messagetemplates.org), extended format strings that capture first-class properties along with the usual message text.

```csharp
var logger = LogManager.GetCurrentClassLogger();

for (var i = 0; i < 10; ++i)
{
    logger.Info("Hello, {Name}, on iteration {Counter}", Environment.UserName, i);
}
```

When the events logged in this snippet are rendered to a file or console, they'll appear just like regular formatted text. In Seq or another structured log data store, you'll see that the original `Name` and `Counter` values are preserved separately:

![EventsInSeq](https://raw.githubusercontent.com/datalust/nlog-targets-seq/dev/asset/nlog-events-in-seq.png)

This makes filtering with expressions such as `Counter > 8` or `Name like 'nb%'` trivial: no regular expressions or log parsing are needed to recover the original values.

The fully-rendered message is there too, so you can still search for text like `"Hello, nblumhardt"` and find the events you'd expect.

### Attaching additional properties

The `target` declaration in _NLog.config_ can be expanded with additional properties:

```xml
  <target name="seq" xsi:type="Seq" serverUrl="http://localhost:5341" apiKey="">
    <property name="ThreadId" value="${threadid}" as="number" />
    <property name="MachineName" value="${machinename}" />
    <property name="Source" value="${logger}" />
  </target>
```

Any properties specified here will be attached to all outgoing events. You can see examples of `ThreadId` and `MachineName` in the screenshot above. The value can be any supported [layout renderer](https://github.com/NLog/NLog/wiki/Layout-Renderers).

### Acknowledgements

The target is based on the earlier [_Seq.Client.NLog_ project](https://github.com/datalust/seq-client), and benefits from many contributions accepted into that repository.

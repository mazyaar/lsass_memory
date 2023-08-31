</section><section class="full_width small-text background-transparent padding-top-small padding-bottom-none" style=""><div class="container-fluid"><div class="row justify-content-lg-center"><div class="col-12"><div class="content-styled"><img decoding="async" loading="lazy" class="alignnone wp-image-25415 lazyloaded" style="margin-bottom: -40px;" src="https://redcanary.com/wp-content/uploads/2020/09/Icon_Alert-Center_Investigation.svg" alt="Analysis Icon" width="100" height="100" data-ll-status="loaded"><h2>Analysis</h2></div></div></div></div></section><section class="full_width small-text background-transparent padding-top-none padding-bottom-small" style=""><div class="container-fluid"><div class="row justify-content-lg-center"><div class="col-12"><div class="content-styled"><h3>Why do adversaries use LSASS Memory?</h3><p>Adversaries commonly abuse the <a href="https://attack.mitre.org/techniques/T1003/001/" target="_blank" rel="noopener noopener">Local Security Authority Subsystem Service</a> (LSASS) to dump credentials for privilege escalation, data theft, and lateral movement. The process is a fruitful target for adversaries because of the sheer amount of sensitive information it stores in memory. Upon starting up, LSASS contains valuable authentication data such as:</p><ul><li>encrypted passwords</li><li>NT hashes</li><li>LM hashes</li><li>Kerberos tickets</li></ul><p>The LSASS process is typically the first that adversaries target to obtain credentials. Post-exploitation frameworks like <a href="/threat-detection-report/threats/cobalt-strike/" target="_blank" rel="noopener noopener">Cobalt Strike</a> import and customize existing code from credential theft tools like <a href="/threat-detection-report/threats/mimikatz/" target="_blank" rel="noopener noopener">Mimikatz</a>, allowing operators to easily access LSASS via beacons.</p><h3>How do adversaries use LSASS Memory?</h3><p>Adversaries use a variety of tools and methods to dump or scan the process memory space of LSASS. Whatever method they choose, the ultimate goal is to obtain credentials, move laterally, and access valuable systems. In the abstract, LSASS abuse can be categorized broadly into two substantially overlapping categories:</p><ul><li>native processes</li><li>custom adversary tools</li></ul><p>The tooling that adversaries use to extract credentials from LSASS Memory exists on a spectrum ranging from legitimate to dual-purpose to overtly malicious. More often than not, adversaries drop and execute trusted administrative tools onto their target, so we’ll organize our analysis going from legitimate to ambiguous to malicious—starting with processes.</p><p>The Windows Task Manager (<code>taskmgr.exe</code>) and the Windows DLL Host (<code>rundll32.exe</code>) are the two built-in utilities that adversaries seem to abuse most often. Task Manager is capable of dumping arbitrary process memory if executed under a privileged user account. It’s as simple as right-clicking on the LSASS process and hitting “Create Dump File.” The Create Dump File calls the <code>MiniDumpWriteDump</code> function implemented in <code>dbghelp.dll</code> and <code>dbgcore.dll</code>. Additionally, <a href="/threat-detection-report/techniques/rundll32/" target="_blank" rel="noopener noopener">Rundll32</a> can execute the Windows native DLL <code>comsvcs.dll</code>, which exports a function called <code>MiniDump</code>. When this export function is called, adversaries can feed in a process ID such as LSASS and create a MiniDump file.</p><p>Adversaries frequently co-opt a number of Sysinternals tools to access the memory contents of LSASS. A few of the standouts include: <a href="https://docs.microsoft.com/en-us/sysinternals/downloads/procdump" target="_blank" rel="noopener noopener">Sysinternals Procdump</a>, <a href="https://docs.microsoft.com/en-us/sysinternals/downloads/process-explorer" target="_blank" rel="noopener noopener">Sysinternals Process Explorer</a>, and Microsoft’s <a href="https://docs.microsoft.com/en-us/troubleshoot/sql/tools/use-sqldumper-generate-dump-file" target="_blank" rel="noopener noopener">SQLDumper.exe</a>.</p><p>&nbsp;</p><h3>Associated threats</h3><p>We aren’t always able to reliably differentiate when an offensive security tool is used by a red team or an adversary. In fact, as much as a quarter of our detections may be triggered by <a href="https://redcanary.com/threat-detection-report/trends/adversary-emulation-testing/" target="_blank" rel="noopener noopener">sanctioned tests</a>, so we detect the following irrespective of intent. That said, the LSASS-abusing tools we commonly see include:</p><ul><li><a href="https://redcanary.com/threat-detection-report/threats/mimikatz/" target="_blank" rel="noopener noopener">Mimikatz</a></li><li><a href="https://redcanary.com/threat-detection-report/threats/cobalt-strike/" target="_blank" rel="noopener noopener">Cobalt Strike</a></li><li><a href="https://redcanary.com/threat-detection-report/threats/impacket/" target="_blank" rel="noopener noopener">Impacket</a></li><li>Metasploit</li><li>PowerSploit</li><li>Empire</li><li>Pwdump</li><li>Dumpert</li></ul><p>Other threats that have abused LSASS Memory include <a href="https://redcanary.com/threat-detection-report/threats/trickbot/" target="_blank" rel="noopener noopener">TrickBot</a>, Zoremov, and <a href="/threat-detection-report/threats/rose-flamingo">Rose Flamingo</a>.</p></div></div></div></div></section><section class="pagebuilder-anchor" id="mitigation"></section><section class="full_width  background-transparent padding-top-small padding-bottom-small" style=""><div class="container-fluid"><div class="row justify-content-lg-center"><div class="col-12"><h6 class="section-label color-red">take action</h6><div class="content-styled"><p>Microsoft’s additional LSA protections that prevent code injection into LSASS remain among the best mitigation controls for protecting LSASS Memory. In brief, this configuration setting will only allow protected processes to inject into or read the memory content of LSASS.</p></div></div></div></div></section><section class="pagebuilder-anchor" id="detection"></section><section class="pagebuilder-anchor" id="visibility"></section><section class="full_width small-text background-transparent padding-top-small padding-bottom-none" style=""><div class="container-fluid"><div class="row justify-content-lg-center"><div class="col-12"><div class="content-styled"><img decoding="async" loading="lazy" class="alignnone size-full wp-image-30374 lazyloaded" style="margin-bottom: -40px;" src="https://redcanary.com/wp-content/uploads/2022/02/trust_privacy_icon_trans.png" alt="Visibility icon" width="100" height="100" sizes="(max-width: 100px) 100vw, 100px" srcset="https://redcanary.com/wp-content/uploads/2022/02/trust_privacy_icon_trans.png 500w, https://redcanary.com/wp-content/uploads/2022/02/trust_privacy_icon_trans-300x300.png 300w, https://redcanary.com/wp-content/uploads/2022/02/trust_privacy_icon_trans-150x150.png 150w" data-ll-status="loaded"><h2>Visibility</h2><p>&nbsp;</p></div></div></div></div></section><section class="full_width small-text background-transparent padding-top-none padding-bottom-small" style=""><div class="container-fluid"><div class="row justify-content-lg-center"><div class="col-12"><div class="content-styled"><p><em>Note: The visibility sections in this report are mapped to MITRE ATT&amp;CK <a href="https://attack.mitre.org/datasources/" target="_blank" rel="noopener noopener">data sources and components</a>.</em></p><p>As we did for many of the prevalent techniques in this report, we developed detection logic for LSASS Memory by understanding what is normal behavior. Monitoring processes and command lines via enterprise EDR or open source tools like <a href="https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon" target="_blank" rel="noopener noopener">Sysmon</a> is among the best ways to learn what normal—and by extension, abnormal—looks like. On top of the process and command-line data, file and network monitoring offer valuable visibility into memory dumps and process injection.</p><h4>Process monitoring</h4><p>LSASS rarely spawns <a href="https://redcanary.com/blog/child-processes/" target="_blank" rel="noopener noopener">child processes</a> for legitimate reasons. Given that detection based on cross-process events or network connections can generate a lot of noise, <a href="https://redcanary.com/blog/process-creation/" target="_blank" rel="noopener noopener">monitoring for child processes</a>&nbsp;spawning from LSASS is a great starting point for detecting LSASS memory theft.</p><h4>Process access monitoring</h4><p>Cross-process events offer some of the best telemetry for observing and detecting LSASS Memory abuse. It can be a challenge to track and investigate all the processes that are injected into LSASS, in part because some benign software products—like antivirus and password policy enforcement software, for example—have legitimate reasons to access and scan LSASS.</p><h4>File monitoring</h4><p>Adversaries often seek to empty the memory space of LSASS into a <code>.dmp</code> file. Thus, file monitoring can be an effective telemetry source for gaining visibility into this variety of credential theft. More specifically, password-cracking tools Dumpert and SafetyKatz place dump files in predictable file paths, which can enable reliable detection opportunities.</p></div></div></div></div></section><section class="pagebuilder-anchor" id="collection"></section><section class="full_width small-text background-transparent padding-top-small padding-bottom-none" style=""><div class="container-fluid"><div class="row justify-content-lg-center"><div class="col-12"><div class="content-styled"><img decoding="async" loading="lazy" class="alignnone size-full wp-image-12403 lazyloaded" style="margin-bottom: -40px;" src="https://redcanary.com/wp-content/uploads/2019/02/icon_tbd-3.svg" alt="Collection Icon" width="100" height="100" data-ll-status="loaded"><h2>Collection</h2><p>&nbsp;</p></div></div></div></div></section><section class="full_width small-text background-transparent padding-top-none padding-bottom-small" style=""><div class="container-fluid"><div class="row justify-content-lg-center"><div class="col-12"><div class="content-styled"><p><em>Note: The collection sections of this report showcase specific log sources from Windows events, Sysmon, and elsewhere that you can use to collect relevant security information. </em></p><h4>LSASS System Access Control List (SACL) auditing</h4><p>Added in Windows 10, LSASS SACL auditing records all processes that attempt to access <code>lsass.exe</code>, providing valuable information when an adversary attempts to steal credentials from process memory. You can enable this feature within Windows’ advanced policy configuration settings, according to <a href="https://docs.microsoft.com/en-gb/windows/whats-new/whats-new-windows-10-version-1507-and-1511#bkmk-lsass" target="_blank" rel="noopener noopener">Microsoft’s documentation</a>.</p><h4>Sysmon Event ID 10: ProcessAccess</h4><p><a href="https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon" target="_blank" rel="noopener noopener">Sysmon ProcessAccess events</a> log whenever one process attempts to access another. As we’ve discussed throughout this analysis, LSASS abuse often involves a process accessing LSASS to dump its memory contents. In fact, this is so common that Microsoft uses LSASS abuse as an example in its <a href="https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon" target="_blank" rel="noopener noopener">documentation for this data source</a>.</p><h4><strong>Sysmon Event ID 7: Image Loaded</strong></h4><p>Image load events will log whenever a DLL is loaded by a specific process. This may provide useful visibility into adversaries abusing DLLs to dump credentials.</p><h4><strong>Sysmon Event ID 11: FileCreate</strong></h4><p>File creation events are a useful source of telemetry if you want to keep an eye on adversaries emptying the memory space of LSASS and creating credential dump files.</p></div></div></div></div></section><section class="full_width small-text background-transparent padding-top-small padding-bottom-none" style=""><div class="container-fluid"><div class="row justify-content-lg-center"><div class="col-12"><div class="content-styled"><img decoding="async" loading="lazy" class="alignnone size-full wp-image-20884 lazyloaded" style="margin-bottom: -40px;" src="https://redcanary.com/wp-content/uploads/2020/01/icon_threat-detection.svg" alt="Icon-threat detection" width="100" height="100" data-ll-status="loaded"><h2>Detection opportunities</h2><p>&nbsp;</p></div></div></div></div></section><section class="full_width small-text background-transparent padding-top-none padding-bottom-none" style=""><div class="container-fluid"><div class="row justify-content-lg-center"><div class="col-12"><div class="content-styled"><p>The days of detecting LSASS-abusing tools like Mimikatz via traditional methods like antivirus, common command-line arguments, and binary metadata are far behind us. Instead, start at a high level and gather what normal LSASS activity looks like before writing detection logic around abnormal behavior. We’ve got more than 20 detectors that look for various flavors of LSASS abuse. The following logic summarizes some of our most effective analytics.</p><p><em>Note: These detection analytics may require tuning.</em></p><h4>Abnormal LSASS process access and injection</h4><p>One of the best ways to detect adversaries abusing LSASS is to understand what tools or processes routinely access LSASS Memory for legitimate reasons—and then build detection logic for anything that deviates from that. It’s highly unusual for many processes to open a handle into <code>lsass.exe</code>. The following is a generic example of a detection opportunity built around obviously suspicious cross-process events. Security teams should be able to apply this logic to catch equally suspicious activity.</p></div></div></div></div></section><section class="code_snippet background-transparent padding-top-custom padding-bottom-custom" style="padding-top: 20px; padding-bottom: 20px; "><div class="container-fluid"><div class="row justify-content-md-center"><div class="col-12"><div class="content-styled"><pre class="bg-grey width-full"><code class="language-html hljs xml">process == ('powershell.exe' || 'taskmgr.exe' || 'rundll32.exe' || 'procdump.exe' || 'procexp.exe' || [other native processes that don’t normally access LSASS])
&amp;&amp;
cross_process_handle_to ('lsass.exe')</code></pre></div></div></div></div></section> <link media="all" onload="this.onload=null;this.media='all';" rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/highlight.js/10.6.0/styles/default.min.css"> <script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/10.6.0/highlight.min.js"></script> <script>hljs.highlightAll();</script><section class="full_width small-text background-transparent padding-top-none padding-bottom-none" style=""><div class="container-fluid"><div class="row justify-content-lg-center"><div class="col-12"><div class="content-styled"><p>The following example demonstrates <code>powershell.exe</code> obtaining a suspicious handle (requesting <a href="https://docs.microsoft.com/en-us/windows/win32/procthread/process-security-and-access-rights" target="_blank" rel="noopener noopener">PROCESS_ALL_ACCESS</a> – 0x1F0FFF) to <code>lsass.exe</code>:</p></div></div></div></div></section><section class="code_snippet background-transparent padding-top-custom padding-bottom-custom" style="padding-top: 20px; padding-bottom: 20px; "><div class="container-fluid"><div class="row justify-content-md-center"><div class="col-12"><div class="content-styled"><pre class="bg-grey width-full"><code class="language-html hljs xml">$Handle = [Uri].Assembly.GetType('Microsoft.Win32.NativeMethods')::OpenProcess(0x1F0FFF, $False, (Get-Process lsass).Id)</code></pre></div></div></div></div></section> <link media="all" onload="this.onload=null;this.media='all';" rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/highlight.js/10.6.0/styles/default.min.css"> <script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/10.6.0/highlight.min.js"></script> <script>hljs.highlightAll();</script><section class="full_width small-text background-transparent padding-top-none padding-bottom-none" style=""><div class="container-fluid"><div class="row justify-content-lg-center"><div class="col-12"><div class="content-styled"><p>The following example demonstrates a likely benign instance of <code>powershell.exe</code> obtaining a handle to <code>lsass.exe</code> by accessing the <a href="https://docs.microsoft.com/en-us/dotnet/api/system.diagnostics.process.handle?view=net-6.0" target="_blank" rel="noopener noopener">Handle property</a> of a process object as the result of running the <a href="https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-process" target="_blank" rel="noopener noopener">Get-Process cmdlet</a>:</p></div></div></div></div></section><section class="code_snippet background-transparent padding-top-custom padding-bottom-custom" style="padding-top: 20px; padding-bottom: 20px; "><div class="container-fluid"><div class="row justify-content-md-center"><div class="col-12"><div class="content-styled"><pre class="bg-grey width-full"><code class="language-html hljs xml">(Get-Process lsass).Handle</code></pre></div></div></div></div></section> <link media="all" onload="this.onload=null;this.media='all';" rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/highlight.js/10.6.0/styles/default.min.css"> <script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/10.6.0/highlight.min.js"></script> <script>hljs.highlightAll();</script><section class="full_width small-text background-transparent padding-top-none padding-bottom-none" style=""><div class="container-fluid"><div class="row justify-content-lg-center"><div class="col-12"><div class="content-styled"><p>The following example demonstrates using <a href="https://docs.microsoft.com/en-us/sysinternals/downloads/procdump" target="_blank" rel="noopener noopener">ProcDump</a> to dump <code>lsass.exe</code> memory:</p></div></div></div></div></section><section class="code_snippet background-transparent padding-top-custom padding-bottom-custom" style="padding-top: 20px; padding-bottom: 40px; "><div class="container-fluid"><div class="row justify-content-md-center"><div class="col-12"><div class="content-styled"><pre class="bg-grey width-full"><code class="language-html hljs xml">procdump -accepteula -ma lsass</code></pre></div></div></div></div></section> <link media="all" onload="this.onload=null;this.media='all';" rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/highlight.js/10.6.0/styles/default.min.css"> <script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/10.6.0/highlight.min.js"></script> <script>hljs.highlightAll();</script><section class="full_width small-text background-transparent padding-top-none padding-bottom-none" style=""><div class="container-fluid"><div class="row justify-content-lg-center"><div class="col-12"><div class="content-styled"><h4>Rundll32 dumping credentials with <code>MiniDump</code> function</h4><p>As we discussed in the analysis section above and in our analysis of <a href="/threat-detection-report/techniques/rundll32/" target="_blank" rel="noopener noopener">Rundll32</a>, adversaries can create a <code>MiniDump</code> file containing credentials by using <code>rundll32.exe</code> to execute the <code>MiniDumpW</code> function in <code>comsvcs.dll</code> and feeding it the LSASS process ID. To detect this behavior, you can monitor for the execution of a process that seems to be <code>rundll32.exe</code> along with a command line containing the term <code>MiniDump</code>.</p></div></div></div></div></section><section class="code_snippet background-transparent padding-top-custom padding-bottom-custom" style="padding-top: 20px; padding-bottom: 20px; "><div class="container-fluid"><div class="row justify-content-md-center"><div class="col-12"><div class="content-styled"><pre class="bg-grey width-full"><code class="language-html hljs xml">process == rundll32.exe
&amp;&amp;
command_line_includes ('MiniDump')</code></pre></div></div></div></div></section> <link media="all" onload="this.onload=null;this.media='all';" rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/highlight.js/10.6.0/styles/default.min.css"> <script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/10.6.0/highlight.min.js"></script> <script>hljs.highlightAll();</script><section class="full_width small-text background-transparent padding-top-none padding-bottom-none" style=""><div class="container-fluid"><div class="row justify-content-lg-center"><div class="col-12"><div class="content-styled"><p>The following example dumps <code>lsass.exe</code> process memory using <code>rundll32.exe</code>:</p></div></div></div></div></section><section class="code_snippet background-transparent padding-top-custom padding-bottom-custom" style="padding-top: 20px; padding-bottom: 40px; "><div class="container-fluid"><div class="row justify-content-md-center"><div class="col-12"><div class="content-styled"><pre class="bg-grey width-full"><code class="language-html hljs xml">powershell.exe -c "rundll32.exe C:\Windows\System32\comsvcs.dll, MiniDump "$((get-process lsass).id)" $PWD\lsass.dmp full"</code></pre></div></div></div></div></section> <link media="all" onload="this.onload=null;this.media='all';" rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/highlight.js/10.6.0/styles/default.min.css"> <script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/10.6.0/highlight.min.js"></script> <script>hljs.highlightAll();</script><section class="full_width small-text background-transparent padding-top-none padding-bottom-none" style=""><div class="container-fluid"><div class="row justify-content-lg-center"><div class="col-12"><div class="content-styled"><h4>Abnormal LSASS child and parent process relationships</h4><p>Monitoring LSASS child processes is critical because it doesn’t typically spawn additional processes (other than occasional instances of <code>conhost.exe</code>). Our detectors for suspicious LSASS process ancestry have helped us catch a large amount of coinminer activity, along with ransomware threats. The following pseudo ]-detection analytic is a good starting point to detect LSASS abuse:</p></div></div></div></div></section><section class="code_snippet background-transparent padding-top-custom padding-bottom-custom" style="padding-top: 20px; padding-bottom: 20px; "><div class="container-fluid"><div class="row justify-content-md-center"><div class="col-12"><div class="content-styled"><pre class="bg-grey width-full"><code class="language-html hljs xml">process == lsass.exe
&amp;&amp;
child_process == ('cmd.exe' || 'powershell.exe' || 'regsvr32.exe' || 'mstsc.exe' || 'dllhost.exe' || [unsigned binaries])</code></pre></div></div></div></div></section> <link media="all" onload="this.onload=null;this.media='all';" rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/highlight.js/10.6.0/styles/default.min.css"> <script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/10.6.0/highlight.min.js"></script> <script>hljs.highlightAll();</script><section class="full_width small-text background-transparent padding-top-none padding-bottom-none" style=""><div class="container-fluid"><div class="row justify-content-lg-center"><div class="col-12"><div class="content-styled"><p>Parent process monitoring is also important. The following might help detect LSASS activity involving suspicious parent processes:</p></div></div></div></div></section><section class="code_snippet background-transparent padding-top-custom padding-bottom-custom" style="padding-top: 20px; padding-bottom: 40px; "><div class="container-fluid"><div class="row justify-content-md-center"><div class="col-12"><div class="content-styled"><pre class="bg-grey width-full"><code class="language-html hljs xml">parent_process == ('explorer.exe' || 'cmd.exe' || 'lsass.exe' || [renamed or relocated system binaries] || [unsigned binaries])
&amp;&amp;
process == lsass.exe</code></pre></div></div></div></div></section> <link media="all" onload="this.onload=null;this.media='all';" rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/highlight.js/10.6.0/styles/default.min.css"> <script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/10.6.0/highlight.min.js"></script> <script>hljs.highlightAll();</script><section class="full_width small-text background-transparent padding-top-none padding-bottom-small" style=""><div class="container-fluid"><div class="row justify-content-lg-center"><div class="col-12"><div class="content-styled"><h4>Identity-based controls</h4><p>LSASS typically runs with SYSTEM-level privileges, and therefore, security teams can detect malicious use by identifying instances of LSASS running under any non-privileged user context. In order to do this, you’ll need the ability to collect users’ <a href="https://docs.microsoft.com/en-us/windows/security/identity-protection/access-control/security-identifiers" target="_blank" rel="noopener noopener">security identifiers</a> (SID) for newly launched processes. Most endpoint monitoring solutions provide this as a metadata attribute associated with a process start record. Look at instances of LSASS running without a User SID including S-1-5-18.</p><h4>Attack Surface Reduction (ASR): Block credential stealing from LSASS</h4><p>The <a href="https://docs.microsoft.com/en-us/windows/security/threat-protection/microsoft-defender-atp/attack-surface-reduction#block-credential-stealing-from-the-windows-local-security-authority-subsystem" target="_blank" rel="noopener noopener">Microsoft ASR rule</a> for blocking credential theft is designed to prevent certain applications from extracting credentials from LSASS Memory.</p><h3>Weeding out false positives</h3><p>LSASS establishes a lot of cross-process memory injection stemming from itself. We identify far fewer false positives from processes injecting into LSASS. Some password-protection and antivirus products will scan LSASS to evaluate user passwords. If approved by your help desk or IT support, these applications should be added to an allowlist as part of a <a href="https://redcanary.com/blog/tuning-detectors/" target="_blank" rel="noopener noopener">continuous tuning process</a>. Detection logic should be routinely maintained with constant tuning to prevent alert overload. Your analytics should act as a living, breathing code repository with frequent, on-the-fly adjustments to navigate your evolving environment.</p></div></div></div></div></section><section class="pagebuilder-anchor" id="testing"></section><section class="full_width  background-transparent padding-top-small padding-bottom-none" style=""><div class="container-fluid"><div class="row justify-content-lg-center"><div class="col-12"><div class="content-styled"><img decoding="async" loading="lazy" class="alignnone size-full wp-image-13087 lazyloaded" style="margin-bottom: -40px;" src="https://redcanary.com/wp-content/uploads/2019/01/icon_research.svg" alt="Testing Icon" width="100" height="100" data-ll-status="loaded"><h2>Testing</h2><p>Start testing your defenses against LSASS Memory using <a href="https://atomicredteam.io/" target="_blank" rel="noopener noopener">Atomic Red Team</a>—an open source testing framework of small, highly portable detection tests mapped to MITRE ATT&amp;CK.</p><h4>Getting started</h4><p><a href="https://atomicredteam.io/credential-access/T1003.001/" target="_blank" rel="noopener noopener">View atomic tests for T1003.001: LSASS Memory</a>. In most environments, these should be sufficient to generate a useful signal for defenders.</p><h5>Run <a href="https://atomicredteam.io/credential-access/T1003.001/#atomic-test-2---dump-lsassexe-memory-using-comsvcsdll" target="_blank" rel="noopener noopener">this test</a> on a Windows system using an elevated PowerShell prompt:</h5></div></div></div></div></section><section class="code_snippet background-transparent padding-top-custom padding-bottom-custom" style="padding-top: 20px; padding-bottom: 40px; "><div class="container-fluid"><div class="row justify-content-md-center"><div class="col-12"><div class="content-styled"><pre class="bg-grey width-full"><code class="language-powershell">rundll32.exe C:\windows\System32\comsvcs.dll, MiniDump ((Get-Process lsass).Id) C:\Windows\Temp\lsass.dmp full</code></pre></div></div></div></div></section> <link media="all" onload="this.onload=null;this.media='all';" rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/highlight.js/10.6.0/styles/default.min.css"> <script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/10.6.0/highlight.min.js"></script> <script>hljs.highlightAll();</script><section class="full_width  background-transparent padding-top-none padding-bottom-none" style=""><div class="container-fluid"><div class="row justify-content-lg-center"><div class="col-12"><div class="content-styled"><p><strong>What to expect</strong></p><p><code>rundll32.exe</code> will write a full <code>lsass.exe</code> process dump to <code>C:\Windows\Temp\lsass.dmp</code>. Running from an elevated PowerShell prompt will automatically populate the <code>lsass.exe</code> process ID, so that it doesn’t have to be entered manually.</p><h5>Useful telemetry will include:</h5></div></div></div></div></section><section class="table background-transparent padding-top-small padding-bottom-small" style=""><div class="container-fluid"><div class="row justify-content-md-center"><div class="col-12"><table><thead><tr><th>Visibility</th><th>Telemetry</th><th>Collection</th></tr></thead><thead></thead><tbody><tr><td class="center-"><span class="table-label">Visibility :</span><div class="content-styled"><p><strong>Process monitoring</strong></p></div></td><td class="center-"><span class="table-label">Telemetry:</span><div class="content-styled"><p>A <code>rundll32.exe</code> process will start.</p></div></td><td class="center-"><span class="table-label">Collection :</span><div class="content-styled"><p>EDR, Sysmon Event IDs 1 and 10, and Windows Event ID 4688 should collect relevant telemetry</p></div></td></tr><tr><td class="center-"><span class="table-label">Visibility :</span><div class="content-styled"><p><strong>Command monitoring</strong></p></div></td><td class="center-"><span class="table-label">Telemetry:</span><div class="content-styled"><p>Command-line logging will capture the context of what is executed.</p></div></td><td class="center-"><span class="table-label">Collection :</span><div class="content-styled"><p>EDR, Sysmon Event ID 1, and Windows Event ID 4688 should collect relevant telemetry.</p></div></td></tr><tr><td class="center-"><span class="table-label">Visibility :</span><div class="content-styled"><p><strong>Module monitoring</strong></p></div></td><td class="center-"><span class="table-label">Telemetry:</span><div class="content-styled"><p><code>comsvcs.dll</code> will load in the <code>rundll32.exe</code> process.</p></div></td><td class="center-"><span class="table-label">Collection :</span><div class="content-styled"><p>EDR and Sysmon Event ID 7 should collect relevant telemetry.</p></div></td></tr><tr><td class="center-"><span class="table-label">Visibility :</span><div class="content-styled"><p><strong>File monitoring</strong></p></div></td><td class="center-"><span class="table-label">Telemetry:</span><div class="content-styled"><p><code>rundll32.exe</code> will write the <code>lsass.exe</code> process dump to <code>C:\Windows\Temp\lsass.dmp</code>.</p></div></td><td class="center-"><span class="table-label">Collection :</span><div class="content-styled"><p>EDR and Sysmon Event ID 11 should collect relevant telemetry.</p></div></td></tr></tbody></table></div></div></div></section><section class="full_width  background-transparent padding-top-none padding-bottom-large" style=""><div class="container-fluid"><div class="row justify-content-lg-center"><div class="col-12"><div class="content-styled"><h4>Review and repeat</h4><p>Now that you have executed one or several common tests and checked for the expected results, it’s useful to answer some immediate questions:</p><ul><li>Were any of your actions detected?</li><li>Were any of your actions blocked or prevented?</li><li>Were your actions visible in logs or other defensive telemetry?</li></ul><p>Repeat this process, performing additional tests related to this technique. You can also <a href="https://github.com/redcanaryco/atomic-red-team/wiki/Getting-Started" target="_blank" rel="noopener noopener">create and contribute</a> tests of your own.</p></div></div></div></div></section></div></main><section class="footer-cta rocket-lazyload lazyloaded" style="background-image: url(&quot;https://redcanary.com/wp-content/uploads/2022/12/globe-white-right-1.webp&quot;);" data-ll-status="loaded"><div class="container-fluid"><div class="row"><div class="col-md-1 spacer">&nbsp;</div></div>
Source: https://redcanary.com/threat-detection-report/techniques/lsass-memory/

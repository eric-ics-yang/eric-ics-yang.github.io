### Meltdown Spectre 

CPU熔断（Meltdown）和幽灵（Spectre）漏洞

熔断漏洞利用CPU乱序执行技术的设计缺陷，破坏了内存隔离机制，使恶意程序可越权访问操作系统内存数据，造成敏感信息泄露。

幽灵漏洞利用了CPU推测执行技术的设计缺陷，破坏了不同应用程序间的逻辑隔离，使恶意应用程序可能获取其它应用程序的私有数据，造成敏感信息泄露。

熔断漏洞设计几乎所有的Intel CPU和部分ARM CPU；幽灵漏洞设计所有的Intel CPU、AMD CPU，以及部分ARM CPU。

两者均属于芯片级漏洞，来源于硬件，需要从CPU架构和指令执行机理层面进行修复。主要影响和风险包括窃取内存数据、造成敏感信息泄漏等。

部分厂商安全公告和补丁链接[参考]：

Intel

https://security-center.intel.com/

https://newsroom.intel.cn/press-kits/security-exploits-intel-products/

AMD

http://www.amd.com/en/corporate/speculative-execution

ARM

https://developer.arm.com/support/security-update

NVIDIA

http://nvidia.custhelp.com/app/answers/detail/a_id/4611

微软（操作系统）

https://portal.msrc.microsoft.com/zh-cn/security-guidance/advisory/ADV180002

https://support.microsoft.com/en-us/help/4073119/protect-against-speculative-execution-side-channel-vulnerabilities-in

苹果

https://support.apple.com/zh-cn/HT208394

Android

https://googleprojectzero.blogspot.co.at/2018/01/reading-privileged-memory-with-side.html

https://www.chromium.org/Home/chromium-security/ssca

https://security.googleblog.com/2018/01/todays-cpu-vulnerability-what-you-need.html

微软IE/Edge

https://portal.msrc.microsoft.com/zh-cn/security-guidance/advisory/ADV180002

Firefox

https://blog.mozilla.org/security/2018/01/03/mitigations-landing-new-class-timing-attack/

Chrome

https://www.chromium.org/Home/chromium-security/ssca

Safari

https://support.apple.com/zh-cn/HT208394


  [参考]:http://www.cnbeta.com/articles/tech/690165.htm

# SAST vs. SCA

## What is static application security testing (SAST)?
SAST is a type of white-box testing leveraged before an application is deployed to analyze source code for security risks. By breaking down the code into different source components, SAST exposes potential vulnerabilities like SQL injection, cross-site scripting (XSS), and buffer overflows.

SAST tools analyze various static factors, such as design documents, specifications, and requirements, to assess the potential security vulnerabilities in an application. This detailed information helps development teams prioritize which code vulnerabilities to address first.

Examples of commonly used SAST tools include Cycode, Checkmarx, Fortify, and Aikido Security.

### Advantages of SAST
Shift-left security: SAST enables developers to identify and fix security issues early in the development cycle, reducing the possibility of large-scale security breaches and significantly strengthening an organization’s security posture. 

Secure coding: Using SAST promotes secure coding practices and helps developers write more robust code. For example, a developer could be writing a function to handle user input. Without SAST, they might accidentally introduce a buffer overflow vulnerability by not properly validating the length of the input. However, a SAST tool would flag this as a potential security issue, prompting the developer to revise their code to include input validation checks. This helps prevent attackers from exploiting the vulnerability to execute malicious code.

Compliance: Several industries are required to adhere to regulatory standards for software security, including PCI DSS, FedRAMP, FISMA, SOC 2, and HIPAA. With SAST, organizations can meet the mandated requirements at the source-code level. SAST also facilitates documentation and audit trails as proof of compliance. These processes both eliminate the risk of legal penalties and also foster customer trust.

Contextual, prioritized results: One huge advantage of SAST? The testing method provides comprehensive feedback on all potential risks identified and their severity. This way, development teams can determine what to address first.

### Limitations of SAST
Though SAST has many benefits, it has a few downsides too:

Coverage gaps: SAST solutions can only detect vulnerabilities that exist in the code itself, which means there’s no complete coverage for external dependencies. Also, SAST doesn’t detect vulnerabilities in runtime applications. 

Different SAST requests for every coding language: Organizations that use more than one code language will need a different instance of SAST for each language. Because each SAST instance requires different maintenance and configuration processes, operating costs may stack up.

False positives: One major limitation of SAST solutions is that they are prone to false alarms. When scanning results return false positives, it can lead to alert fatigue.

Requires access to source code: You may not have access to the application’s source code, and without source code, SAST won’t work well—or at all. 

## Software composition analysis (SCA)
SCA identifies and manages security risks found in third-party software components, libraries, binary code, and dependencies used in the development of a software application.

In other words, software composition analysis identifies code that the development team didn’t create. SCA tools then create a comprehensive inventory of dependencies, scan them against known vulnerability databases, and alert developers to potential risks. SCA solutions also help track license compliance to ensure the legal usage of open-source components.

Next, an SCA tool generates a report highlighting vulnerabilities, license risks, and other issues, along with recommendations for remediation. The report and recommendations are integrated into the development workflow, allowing developers to address issues before deployment.

Some examples of popular SCA tools are Wiz, JFrog Xray, and Xygeni.

### Advantages of SCA
Efficiency: SCA tracks and identifies known vulnerabilities in open-source components quickly and efficiently—at the same time that development teams write code.

Automation: Many SCA tools offer automated remediation options for identified vulnerabilities.

Dependency management: SCA helps organizations manage their third-party dependencies, ensuring that they are using the latest versions and avoiding outdated or vulnerable components.

License compliance: SCA can help track license compliance for open-source components, preventing legal issues and ensuring that the organization is using components in accordance with their licensing terms.

### Limitations of SCA
Just like SAST, SCA has a few drawbacks:

Ownership of risk: Components with flagged vulnerabilities can belong to different teams and projects, so when these risks are identified, it might be a challenge to determine who should take responsibility for fixing the security issue. This can lead to confusion and delays in addressing security issues, especially when teams are alerted to a large number of potential risks. 

False positives: As mentioned above, SCA tools tend to generate long lists of potential risks, which may include irrelevant risks and false positives. Teams that review SCA results manually might waste extensive resources that could have been spent assessing real risks. 

Technical debts: Technical debts are incurred when secure coding practices aren’t prioritized from the beginning of the software development lifecycle. Technical debts can also arise when libraries or open-source components that were formerly used are abandoned. If left unaddressed, these debts can lead to increased development costs, delayed project timelines, and security vulnerabilities.

Coverage gaps: SCA tools require an up-to-date vulnerability database to be effective. Similarly, software composition analysis solutions may not be capable of identifying every third-party component in use nor every open-source project.

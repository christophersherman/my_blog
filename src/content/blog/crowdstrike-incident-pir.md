---
author: Christopher Sherman 
pubDatetime: 2024-07-25T09:22:00Z
modDatetime: 2024-07-25T20:53:00Z
title: CrowdStrike's outage as a Reliability Engineer
slug: crowdstrike-outage-as-an-sre 
featured: true
draft: false
tags:
  - CrowdStrike
  - outage
  - reliability 
  - incident 
description:
    A reaction to the catastrophic crowdstrike windows outage from the 
    perspective of a professional site reliability engineer.
---

## Intro

On the 19th of July, 2024, at 04:09 UTC, cyber-security giant CrowdStrike released a routine sensor configuration update to their Falcon platform. This update was intended to enhance security measures by targeting newly observed malicious named pipe activities.

It would take only 1 hour and 18 minutes for the company to release a statement admitting there was a crucial error embedded in the logic of their configuration update, causing affected machines to enter
the infamous blue screen of death (BSOD), essentially 'bricking' the machine entirely. The statement by CrowdStrike assured there was already a fix in place for this simple mistake, and assured their customers
that this was not the malicious works of a cyber-security incident. 

The rapid acknowledgment and remediation efforts by CrowdStrike, however, did little to prevent the widespread disruptions that followed. As most of the world was still asleep, unaware of the brewing chaos, the true scale of the disaster was yet to unfold. When morning came, many would wake to find their systems crippled, plunging businesses into operational turmoil and leaving IT teams scrambling to contain the fallout.

## The fallout

The fallout of this disaster was immediately noticeable here in the country of Australia. Due to strict regulatory requirements for cybersecurity, many companies in Australia have adopted Crowdstrike, and its cloud-native platform, Falcon, to help comply with regulations.  

Like a tidal wave, many of the impacted companies started to notice their machines suddenly blue-screening, leaving them 'bricked'. The disaster spread quickly, affecting point-of-sale devices, company personnel computers, petrol station displays, airport information screens, and countless other critical systems, creating widespread chaos and operational paralysis.

The impact in Australia gave an early example of what was coming to the rest of the world in their coming operational hours. Early estimates state 8.5 million devices were impacted, causing up to $5.4 billion in financial losses. The incident resulted in the cancellation of over 5,000 flights globally, and plunged health services into chaos as critical systems shut offline overnight. 


### Understanding the Broader Implications
The global impact of CrowdStrike's misstep was a stark reminder of how interconnected our digital infrastructure has become. The outage did not just affect individual companies; it highlighted vulnerabilities in sectors ranging from healthcare to transportation, where system reliability is paramount. This incident serves as a powerful case study in the potential consequences of insufficient testing and preparation.


## The importance of rigorous testing 
 
The exact details of the "logic error" in the failed configuration update remain unclear. However, the language in media and public statements surrounding the issue, combined with the swift time-to-fix, suggests it was relatively simplistic in nature. CrowdStrike has publicly admitted that the code change wrongfully passed pre-deployment validation steps, indicating a significant oversight in the testing process.



### Best practices for pre-deployment validation 
Ensuring the reliability of software updates requires a multi-faceted approach that integrates various testing and validation practices. 

Unit tests are essential for verifying that individual components of the software work as intended. However, merely achieving high code coverage without ensuring the quality and relevance of the tests can lead to a false sense of security. This was evident in the CrowdStrike outage, where a simplistic logic error slipped through the unit testing phase. Improving unit testing is a hot topic in the realm of software development. I offer my simple advice on the matter to avoid production incidents: 

1. Focus on Test Quality, Not Just Coverage:  
  Avoid being lazy with test cases. Unit testing culture can commonly end up with jaded developers treating it as an annoying hurdle in their release process. Simply aiming for high coverage percentages without ensuring the tests are meaningful can lead to a false sense of security, which can snowball into problems as the code base grows.
2. Regularly Update Unit Tests:  
  Unit tests should evolve with the codebase. Whenever new features are added or existing code is refactored, corresponding unit tests should be updated to reflect these changes.
3. Peer Reviews for Test Cases:
  Implement peer reviews not only for code changes but also for test cases. Involving multiple perspectives on one thing can help catch potential issues. 

While traditional pre-deployment testing such as unit tests are foundational, we should always accomodate for things slipping through the cracks. Additional strategies like canary deployments and robust monitoring can further safeguard against slippery errors. 


### Canary Deployments

Canary deployments involve rolling out updates to a small subset of users or servers before a full-scale deployment. This technique allows organizations to monitor the update’s impact in a real-world environment with minimal risk. If any issues are detected, the update can be halted or rolled back before it affects the broader user base. This staged approach acts as a safeguard, providing an additional layer of verification beyond pre-deployment tests.  

In the example of Crowdstrike's outage on windows machines, a canary release to a small group of windows customers could have significantly minimised the impact of outage. Release engineers could have monitored their canary subset, and noticed the problematic behaviour, halting the release before the wider user-base received the poisoned update. 
## Implementing Robust Rollback Mechanisms 

Even with all the pre-deployment checks and procedures in the world, actually deploying your change to your production systems can still be risky business. Our production systems are likely delicate, with big risk of impact to business operations when disturbed. Sure, it is paramount to increase the likelihood and trust in the deployment process, but it is always necessary to have a plan B. That is where the robust rollback comes into play.  

The process of 'rolling-back' a system is a simple idea. The release engineer might notice a problem with the release (hopefully in a canary subset, or something similar), and they will hit the big red abort button. In a perfect world, there is a simple system in place to return your production systems or servers to a previous, and likely safe version. The potential of a graceful rollback may vary widely dependent on the architecture in place. Microservice-based companies move fast and deploy often, usually in a containerized environment. It is normally trivial to simply revert your cloud-hosted containers back to a previous version. Admittedly, endpoint security software such as CrowdStrike's Falcon Platform may offer some additional complexity when it comes to rollbacks, especially when updates cause critical system failures like blue screens, leaving computers unbootable and unable to be remotely controlled. However, this complexity does not absolve engineers of their responsibility to implement effective rollback mechanisms. Even in extreme cases, it is crucial to have a plan that includes offline recovery options and robust pre-deployment testing to minimize the impact of such failures.

#### Graceful Degredation 
Implementing mechanisms that allow the system to degrade gracefully is a key part of disaster prevention. This approach maintains critical functionality even when some components fail. During the CrowdStrike incident, a system designed for graceful degradation could have kept essential security monitoring functions operational while isolating the affected components. For example, feature flags could be used to disable non-critical features, keeping essential operations running smoothly and giving teams time to address the issues without a complete system outage.

By designing for graceful degradation, you can significantly enhance the robustness of your deployment process. These strategies help ensure that even in the face of unexpected failures, your systems can maintain continuity and minimize the impact on end-users. 

## Effective Communication Strategies During a Crisis 
Pragmatic engineering tells us that despite our best efforts, accidents happen. Humans are humans, and like death and taxes, we can always count on things going wrong. The best defense is preparedness and planning. In the event of a critical outage such as this, it is easy to get swept up in the chaos and turmoil. The root cause of the outage or disturbance is not always immediately evident to internal teams. This can lead to a frantic involvement of teams and engineers, desperately hunting for a starting place in the clean-up process. What is commonly overlooked, or undervalued, is the line of communications. More often than not, a production incident impacts external partners or consumers. There is a responbility that must be honoured to keep our stakeholders in the loop. That means timely acknowledgments, frequent and informative updates, user-friendly advice when applicable, and customer-focused engagement in a post-incident future.  

CrowdStrike was generally effective in their communication during this recent incident. They were adequately quick to acknowledge the issue, but particularly shined in the frequency of their updates. They maintained a robust communication strategy, providing regular updates on their support portal and website. They collaborated with Microsoft to develop and distribute remediation tools in the form of USB-based recovery systems. They provided detailed information to customers about the cause of the outage, includiong technical specifics of how to manually remediate impacted machines. 

## Incident Response and Learning from Mistakes 
Post-incident analysis is essential for preventing future occurrences. CrowdStrike has committed to a thorough root cause analysis to understand how the logic error passed pre-deployment validation steps and to identify workflow improvements. This process includes examining the oversight in testing procedures and implementing more rigorous validation protocols.

Blameless post-mortems focus on understanding and improving systems and processes without assigning personal blame. They encourage open communication, making team members feel safe to discuss mistakes and observations. Detailed documentation of incidents, including timelines and actions taken, is essential for thorough analysis and future reference. Identifying root causes through techniques like the "5 Whys" helps uncover underlying issues, leading to actionable solutions. These solutions should be clearly defined, assigned to owners, and given deadlines. Regular follow-ups ensure that solutions are implemented effectively. Sharing findings and lessons learned with the broader team fosters a culture of continuous improvement, enhancing overall system reliability and promoting a supportive work environment.

### An Optimistic Outlook
CrowdStrike has demonstrated a proactive approach to handling the July 2024 outage by committing to a thorough root cause analysis and remediation efforts. Their swift acknowledgment of the issue, detailed communication, and collaboration with partners like Microsoft highlight their capability to address complex incidents effectively. This proactive stance indicates that CrowdStrike is well-positioned to conduct a comprehensive post-incident review (PIR), leveraging their extensive resources and expertise. By focusing on transparency, continuous improvement, and stakeholder engagement, CrowdStrike can turn this incident into a valuable learning opportunity, further strengthening their processes and enhancing system reliability for the future. Their commitment to understanding and rectifying the logic error that caused the outage reflects a dedication to maintaining high standards of operational integrity and customer trust.


## The Power and Responsibility of Major Companies
With great power comes great responsibility. Companies like CrowdStrike, which have a vast reach and influence over the digital security and operational continuity of countless organizations, bear an immense duty to their customers. This incident underscores the critical need for such companies to uphold the highest standards of reliability and best practices. The ability to safeguard data and maintain operational integrity across industries places a significant onus on these firms to ensure thorough testing, robust safeguards, and proactive measures are in place to prevent such widespread disruptions. Companies must recognize their duty to serve customers with reliable products and services, ensuring they are always prepared to handle the unforeseen challenges of an interconnected world.

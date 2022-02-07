---
title: Teams meeting interoperability
titleSuffix: An Azure Communication Services concept document
description: Join Teams meetings
author: tomkau
ms.author: tomkau
ms.date: 10/15/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: teams-interop
---

# Join a Teams meeting

> [!IMPORTANT]
> BYOI interoperability is now generally available to all Communication Services applications and Teams organizations.

Azure Communication Services can be used to build applications that enable users to join and participate in Teams meetings. [Standard ACS pricing](https://azure.microsoft.com/pricing/details/communication-services/) applies to these users, but there's no additional fee for the interoperability capability itself. With the bring your own identity (BYOI) model, you control user authentication and users of your applications don't need Teams licenses to join Teams meetings. This is ideal for applications that enable licensed Teams users and external users using a custom application to join into a virtual consultation experience. For example, healthcare providers using Teams can conduct teleheath virtual visits with their patients who use a custom application. 

It's also possible to use Teams identities with the Azure Communication Services SDKs. More information is available [here](./teams-interop.md).

Interoperability is not enabled for [Teams for personal use](https://www.microsoft.com/microsoft-teams/teams-for-home).

It's currently not possible for a Teams user to join a call that was initiated using the Azure Communication Services Calling SDK.

## Enabling anonymous meeting join in your Teams tenant

When a BYOI user joins a Teams meeting, they're treated as an anonymous external user, similar to users that join a Teams meeting anonymously using the Teams web application. The ability for BYOI users to join Teams meetings as anonymous users is controlled by the existing "allow anonymous meeting join" configuration. This same configuration also controls the existing Teams anonymous meeting join. This setting can be updated in the [Teams admin center](https://admin.teams.microsoft.com/meetings/settings) or with the Teams PowerShell cmdlet [Set-CsTeamsMeetingConfiguration](/powershell/module/skype/set-csteamsmeetingconfiguration).  

Custom applications built with Azure Communication Services to connect and communicate with Teams users can be used by end users or by bots, and there is no differentiation in how they appear to Teams users unless the developer of the application explicitly indicates this as part of the communication. Your custom application should consider user authentication and other security measures to protect Teams meetings. Be mindful of the security implications of enabling anonymous users to join meetings, and use the [Teams security guide](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings) to configure capabilities available to anonymous users.

## Meeting experience

As with Teams anonymous meeting join, your application must have the meeting link to join, which can be retrieved via the Graph API or from the calendar in Microsoft Teams. The name of BYOI users that is displayed in Teams is configurable via the Communication Services Calling SDK. They are labeled as “external” to let Teams users know they weren't authenticated using Azure Active Directory.

A Communication Service user won't be admitted to a Teams meeting until there is at least one Teams user present in the meeting. Once a Teams user is present, then the Communication Services user will wait in the lobby until explicitly admitted by a Teams user, unless the "Who can bypass the lobby?" meeting policy/setting is set to "Everyone".

During a meeting, Communication Services users will be able to use core audio, video, screen sharing, and chat functionality via Azure Communication Services SDKs. Once a Communication Services user leaves the meeting or the meeting ends, they're no longer able to send or receive new chat messages, but they'll have access to messages sent and received during the meeting. Anonymous Communication Services users can't add/remove participants to/from the meeting nor can they start recording or transcription for the meeting.

Additional information on required dataflows for joining Teams meetings is available at the [client and server architecture page](client-and-server-architecture.md). The [Group Calling Hero Sample](../samples/calling-hero-sample.md) provides example code for joining a Teams meeting from a web application.

## Diagnostics and call analytics
After a Teams meeting ends, diagnostic information about the meeting is available using the [Communication Services logging and diagnostics](/azure/communication-services/concepts/logging-and-diagnostics) and using the [Teams Call Analytics](/MicrosoftTeams/use-call-analytics-to-troubleshoot-poor-call-quality) in the Teams admin center. Communication Services users will appear as "Anonymous" in Call Analytics screens. Communication Services users aren't included in the [Teams real-time Analytics](/microsoftteams/use-real-time-telemetry-to-troubleshoot-poor-meeting-quality).

## Privacy
Interoperability between Azure Communication Services and Microsoft Teams enables your applications and users to participate in Teams calls, meetings, and chat. It is your responsibility to ensure that the users of your application are notified when recording or transcription are enabled in a Teams call or meeting.

Microsoft will indicate to you via the Azure Communication Services API that recording or transcription has commenced and you must communicate this fact, in real time, to your users within your application's user interface. You agree to indemnify Microsoft for all costs and damages incurred as a result of your failure to comply with this obligation.

## Limitations and known issues

- Communication Services users can join a Teams meeting that is scheduled for a Teams channel and use audio and video, but they won't be able to send or receive any chat messages because they aren't members of the channel.
- Communication Services users may join a Teams webinar, but the presenter and attendee roles aren't currently enforced, thus Communication Services users could perform actions not intended for attendees, such as screen sharing, turning their camera on/off, or unmuting themselves, if your application provides UX for those actions.
- When using Microsoft Graph to [list the participants in a Teams meeting](/graph/api/call-list-participants), details for Communication Services users are not currently included.
- PowerPoint presentations aren't rendered for Communication Services users.
- Teams meetings support up to 1000 participants, but the Azure Communication Services Calling SDK currently only supports 350 participants and Chat SDK supports 250 participants. 
- With [Cloud Video Interop for Microsoft Teams](/microsoftteams/cloud-video-interop), some devices have seen issues when a Communication Services user shares their screen.
- [Communication Services voice and video calling events](/azure/event-grid/communication-services-voice-video-events) aren't raised for Teams meeting.
- Features such as reactions, raised hand, together mode, and breakout rooms are only available for Teams users.
- Communication Services users can't interact with poll or Q&A apps in meetings.
- Communication Services won't have access to all chat features supported by Teams. They can send and receive text messages, use typing indicators, read receipts and other features supported by Chat SDK. However features like file sharing, reply or react to a message aren't supported for Communication Services users.   
- The Calling SDK doesn't currently support closed captions for Teams meetings.
- Communication Services users can't join [Teams live events](/microsoftteams/teams-live-events/what-are-teams-live-events).
- [Teams activity handler events](/microsoftteams/platform/bots/bot-basics?tabs=csharp) for bots don't fire when Communication Services users join a Teams meeting.

## Next steps

- [How-to: Join a Teams meeting](../how-tos/calling-sdk/teams-interoperability.md)
- [Quickstart: Join a BYOI calling app to a Teams meeting](../quickstarts/voice-video-calling/get-started-teams-interop.md)
- [Quickstart: Join a BYOI chat app to a Teams meeting](../quickstarts/chat/meeting-interop.md)
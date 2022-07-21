Before using Agora Chat, you need to enable and configure the Agora Chat service at [Agora Console](https://console.agora.io/#onboarding).


## Prerequisites

Before enabling the Agora Chat service, make sure that you have the following:

- A valid [Agora account](https://docs.agora.io/en/AgoraPlatform/get_appid_token?platform=AllPlatforms#create-an-agora-account).
- An [Agora project](https://docs.agora.io/en/AgoraPlatform/get_appid_token?platform=AllPlatforms#create-an-agora-project) that uses  **APP ID + Token** as its authentication.
- An Agora Chat pricing plan. For how to subscribe to a plan, see [Subscribe to the pricing plan](./agora_chat_pricing#subscribe-to-the-pricing-plan).


## Enable the Agora Chat service

1. Log in to the [Agora Console](https://console.agora.io).

2. In the left navigation bar, click **Project Management** and click **Config** for the project that you want to use. 

3. In the **Features** section of the **Edit Project** page, click **Enable** next to **Chat** to enable the service.

Once the Chat service is enabled, you are redirected to the Chat config page. You can then enable the below advanced features of Chat based on your business requirements.

<img width="50%" src="https://web-cdn.agora.io/docs-files/1658310228255" />

<img width="50%" src="https://web-cdn.agora.io/docs-files/1658310318751" />

For details about these advanced features, see the following:
- [Message Callback](./agora_chat_set_up_webhooks)
- [Message Thread](./agora_chat_thread_management_android)
- [Reaction](./agora_chat_reaction_android)
- [Offline Message Push (Advanced)](./agora_chat_push_android)
- [Presence](./agora_chat_presence_android)
- [Translation](./agora_chat_translation_android)
- [Moderation](./agora_chat_moderation_overview)


## Get the information of the Agora Chat project

Agora Console assigns the following information to each project that enables the Agora Chat service:

- **Data Center**: Agora provides several data centers for the service in different regions, including Beijing1 (China), Beijing VIP (China),  Singapore, Frankfurt (Germany), and Virginia (USA). After the plan is changed, the data center remains unchanged.
- **AppKey**: The unique identifier that the Agora Chat service assigns to each app. The rule is `${OrgName}#{AppName}`.
- **OrgName**: The unique identifier that the Agora Chat service assigns to each enterprise (organization).
- **AppName**: The name that the Agora Chat service assigns to each app. Each app under the same enterprise (organization) must have a unique App Name.
- **API request url**: The domain of the WebSocket and RESTful API request that Agora assigns to each project.

Follow these steps to get the project information:

1. Find the project that has enabled the Chat service on the [Project management](https://console.agora.io/projects) page at Agora Console, and click **Config**.
2. On the project edit page, find **Chat** and click **Config**.
3. On the project config page, get the values of **Data Center**, **AppKey**, **OrgName**, **AppName**, **WebSocketAddress**, and **REST API**.


## Next steps

After enabling and configuring Chat Service, the Chat-related features in Agora Analytics are enabled by default to help you keep track the usage trends and quality details. For more information, see [Data Insights](./analytics_agora_chat) and [Data Metrics](./analytics_agora_chat_glossary).
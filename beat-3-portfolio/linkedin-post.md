I opened a documentation fix for Red-DiscordBot, an open source project, to make its contributor setup instructions clearer. I investigated why setup could fail even when a contributor had a supported version of Python installed.

I reproduced the failure and updated the guide to explain which Python command the setup uses and how a contributor can choose another supported one. After a reviewer suggested a formatting improvement, I revised the guide and checked the documentation again.

The pull request is still open and awaiting review. This work showed me how a small gap in instructions can make it harder for someone to contribute, and how useful it is to reproduce a problem before choosing a solution.

https://github.com/Cog-Creators/Red-DiscordBot/pull/6814

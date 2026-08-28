---
title: "Create Custom Visuals in Power BI with GitHub Copilot"
url: "https://dax.tips/2025/09/29/create-custom-visuals-in-power-bi-with-github-copilot"
published: "2025-09-29"
updated: "2026-03-15"
source: "dax.tips"
---

![](https://i0.wp.com/dax.tips/wp-content/uploads/2025/09/Music2.png?resize=867%2C616&ssl=1)

## UPDATE: 15-March-2026

I created a public repo with all my custom visuals, including source code and setup instructions here:  
<https://github.com/dax-tips/CustomVisuals>

## Blog

Last week I had the pleasure to attend the [Power BI Next Step](https://powerbinextstep.com/) conference in Copenhagen. I enjoyed it and can highly recommend it. As part of that event, I was invited to a fun “[Power BI Shootout](https://powerbinextstep.com/2025/09/power-bi-shootout-line-up-revealed/)” session. Each competitor had 10 minutes to show something fun and creative using Power BI. There were four judges and audience voting to decide the eventual winner.

For my entry, I decided to build some games as Power BI Custom Visuals. I want to show how well GitHub Co-pilot helps in this task. This article outlines the steps I followed to set up everything needed. These steps help to quickly create amazing Power BI Custom visuals using Copilot in Visual Studio Code. Once I created the custom visual project, I didn’t write a single line of code!

I began my ten-minute slot by asking the audience a question. How many knew it was possible to create custom visuals for Power BI? Most raised their hands. I followed up by asking how many had *actually* created a Power BI Custom Visual. Very few hands remained raised. I expected this. There are some great tutorials in MS Learn explaining how to build custom visuals. Still, there is still a steep learning curve if you want to create some amazing visuals.

This is where GitHub Co-pilot came in, and boy did it do a great job!

I built a brand-new custom visual from scratch in my ten-minute time slot. No prepared code or instructions. The only work I did to save time was make sure my laptop had clean versions of node.js and pbiviz preinstalled.

Here are the steps you need to follow to get this working yourself. Please note, I have a Windows 11 machine. These steps may not work for you if you have a different operating system.

1. Start over at [MS Learn](https://www.microsoft.com/en-us/power-platform/products/power-bi/developers/custom-visualization) where there is a tonne of useful information on building custom visuals.
2. [Set up your environment](https://learn.microsoft.com/en-gb/power-bi/developer/visuals/environment-setup?tabs=desktop) which consists of three main steps
   - [Install node.js](https://learn.microsoft.com/en-gb/power-bi/developer/visuals/environment-setup?tabs=desktop#install-nodejs)
   - [Install pbiviz](https://learn.microsoft.com/en-gb/power-bi/developer/visuals/environment-setup?tabs=desktop#install-pbiviz)
   - [Enable Power BI developer mode](https://learn.microsoft.com/en-gb/power-bi/developer/visuals/environment-setup?tabs=service#enable-developer-mode). I enabled developer mode in the web service.
3. Once you have your environment set up, use the command line, or PowerShell terminal to issue the following command.  
     
   `pbiviz new <insert project name>`  
     
   ![](https://i0.wp.com/dax.tips/wp-content/uploads/2025/09/new.png?resize=600%2C311&ssl=1)  
   ![](https://i0.wp.com/dax.tips/wp-content/uploads/2025/09/new1.png?resize=600%2C303&ssl=1)
4. This step will create a new sub-folder using the name of the project. Open the newly created folder in VS Code. You should see several files and folders that make up this project.  
     
   ![](https://i0.wp.com/dax.tips/wp-content/uploads/2025/09/new2.png?resize=600%2C328&ssl=1)  
     
   Back in the command or PowerShell terminal, type the following command. This will start a small web service. It will host a copy of the visual while you develop the code.  
     
   `pbiviz start`  
     
   If you see a [certificate related](https://learn.microsoft.com/en-us/power-bi/developer/visuals/create-ssl-certificate) error related the first time you run, you need to run the following (without double-dashes).  
     
   pbiviz install-cert  
      
   ![](https://i0.wp.com/dax.tips/wp-content/uploads/2025/09/new3.png?resize=600%2C383&ssl=1)
5. Enable developer mode by going into the [Power BI developer settings](https://app.powerbi.com/user/user-settings/developer-settings?experience=power-bi).  
     
   ![](https://i0.wp.com/dax.tips/wp-content/uploads/2025/09/developer-settings-1.png?resize=600%2C205&ssl=1)
6. Create a fresh report in your Power BI Web Service and connect it with some data. You should see the dedicated “Developer” visual among the other native visuals. This visual connects back to the **pbiviz** server you started at step 5.  
     
   The developer visual shows up in the Visualization pane (1). When you add it to the canvas, it should be akin to below. The button at (2) lets you refresh the visual to debug.  
     
   ![](https://i0.wp.com/dax.tips/wp-content/uploads/2025/09/new4.png?resize=600%2C459&ssl=1)
7. Jump back to Visual Studio Code and make sure you have the GitHub Copilot and GitHub Copilot Chat extensions installed.  
     
   ![](https://i0.wp.com/dax.tips/wp-content/uploads/2025/09/vscode.png?resize=600%2C354&ssl=1)
8. Start a new Copilot chat in Agent mode. Start issuing prompts like “Do you understand Typescript?”   
     
   I have access to a subscription to [GitHub Copilot](https://code.visualstudio.com/docs/copilot/overview) through my work. This gives me access to premium models. If you have a subscription or start a trial, I highly recommend using the **Claude Sonnet 4** model. Use it in **Agent mode** for your chat. However, it is fun to experiment with other models.  
     
   [Here is an article](https://docs.github.com/en/copilot/get-started/plans) outlining the various plans available for GitHub Copilot including a free plan to get you started. There is a free 30-day plan for the Pro edition.
9. Finally, issue a prompt asking the agent what you would like to build. I shared a specific prompt with Copilot during my live presentation. It was:  
     
   “*Can you write me a simple game to be used in my Power BI custom visual?*“  
     
   The result was a fully working Snake game with Arrow key control, Collision detection, Score tracking and smooth animation. I have a “Publish to Web” Power BI report later in this article that shows a working version.  
     
   Copilot automatically edited visual.ts and other relevant files while me and the audience watched it work away. It only took a minute or so to generate the first game.  
     
   ![](https://i0.wp.com/dax.tips/wp-content/uploads/2025/09/game1.png?resize=600%2C755&ssl=1)  
   I then issue a second prompt as a voice command through my laptop microphone  
     
   “*That was great. Can you now add a second simple game but give me a selector so that I can pick between the 2 and give me an 80s style boss key?*“  
     
   The result was a fully playable Tetris game. A dropdown was added. If I pressed the “B” key, the screen flipped to an image showing charts. It also displayed other work type items.  
     
   ![](https://i0.wp.com/dax.tips/wp-content/uploads/2025/09/game2.png?resize=600%2C752&ssl=1)  
   While Copilot worked away on the second game, I switched to Power BI Desktop. There, I had a sample of other fun games. I built these games the previous week. To conclude, I demonstrated a Power BI Custom Visual. It allows the selection of MP3 music files. These files play with a graphic equalizer.  
     
   The music player has a [bunch of features](https://github.com/dax-tips/powerbi-music-player#-features) including a DJ mode allowing you to create your own mashup tracks. The visual and source code for the music custom visual can be [found here](https://github.com/dax-tips/powerbi-music-player).   
     
   ![](https://i0.wp.com/dax.tips/wp-content/uploads/2025/09/music1.png?resize=600%2C433&ssl=1)
10. That’s it! The Shootout was a lot of fun. I enjoyed sharing the stage with my fellow competitors. They were all amazing and awesome and showcased some really neat work. I was fortunate to win the event, well Copilot won on my behalf.
11. To share your visuals, edit the pbiviz.json file back in your Visual Studio Code project. Run the following from the command prompt:  
      
    `pbiviz package`  
      
    ![](https://i0.wp.com/dax.tips/wp-content/uploads/2025/09/package.png?resize=600%2C387&ssl=1)  
      
    This will create a file with the pbiviz file extension. It is now ready to add to Power BI Desktop. You can share it with others. Alternatively, load it to the Power BI Visual marketplace.

[Here is a link](https://app.fabric.microsoft.com/view?r=eyJrIjoiYWNiY2M4NTMtODE4OS00YTFjLWJhMjktNzNlYTJkOGYyYjkzIiwidCI6IjRhODZkNWJiLTQxNzMtNDVlZS1iZmQ1LWEzYjU2ZWUyZDNkNSIsImMiOjl9&pageName=fee2815c2924cfc30db1) to open a Power BI report using publish to web. I also have the visuals loaded in the iFrame below.

## Summary

The shootout was designed to showcase fun and inventive uses for Power BI. The same techniques work just as well to create business useful visuals. I have been impressed with the testing I have done so far. These include small multiples, specific formatting, including cross filtering any many other functions.

You might begin with a prompt like this example.  
  
“*Create a bar chart custom visual with cross filtering capability*.”  
  
Next, ask the agent to enhance the visual. Tweak it to suit your needs. Keep chatting until you have the desired visual.

I believe this approach simplifies the creation of your own library of Power BI Custom Visuals. This helps to elevate your Power BI Reports to the next level.

The Copilot agent is great for quickly generating most of the code you need. Depending on your requirements, you can either iterate in Copilot chat to help refine your visual. You can also edit the code yourself to tweak, or even learn some TypeScript.

Let me know how you get on. I’d love to hear if anyone reading this is successful at creating a Custom Visual using the above steps.

5
1
vote

Article Rating

# Pomice

![](https://raw.githubusercontent.com/cloudwithax/pomice/main/banner.jpg)

The modern [Lavalink](https://github.com/freyacodes/Lavalink) wrapper designed for [nextcord](https://github.com/nextcord/nextcord)

![](https://img.shields.io/badge/license-GPL-2f2f2f) ![](https://img.shields.io/badge/python-3.8-2f2f2f) ![](https://img.shields.io/discord/899324069235810315) ![](https://readthedocs.org/projects/pomice/badge/?version=latest)


This library is heavily based off of/uses code from the following libraries:
- [Wavelink](https://github.com/PythonistaGuild/Wavelink)
- [Slate](https://github.com/Axelancerr/slate)
- [Granitepy](https://github.com/twitch0001/granitepy)


# Install
To install the library, you need the lastest version of pip and minimum Python 3.8

> Stable version
```
pip install nextcord-ext-pomice
```

> Unstable version (this one gets more frequent changes)
```
pip install git+https://github.com/techpluscraft/nextcord-ext-pomice
```

# Support

The official documentation is [here](https://nextcord-ext-pomice.readthedocs.io/en/latest/)

<!--You can join our support server [here](https://discord.gg/r64qjTSHG8)-->


# Examples
In-depth examples are located in the examples folder

Here's a quick example:

```py
from nextcord.ext import pomice
import nextcord
import re

from nextcord.ext import commands

URL_REG = re.compile(r'https?://(?:www\.)?.+')

class MyBot(commands.Bot):
    
    def __init__(self) -> None:
        super().__init__(command_prefix='!', activity=nextcord.Activity(type=nextcord.ActivityType.listening, name='to music!'))
        
        self.add_cog(Music(self))
        
    async def on_ready(self) -> None:
        print("I'm online!")
        await self.cogs["Music"].start_nodes()
     
        
class Music(commands.Cog):
    
    def __init__(self, bot) -> None:
        self.bot = bot
        
        self.pomice = pomice.NodePool()
    
    async def start_nodes(self):
        await self.pomice.create_node(bot=self.bot, host='127.0.0.1', port='3030', 
                                     password='youshallnotpass', identifier='MAIN')
        print(f"Node is ready!")


        
    @commands.command(name='join', aliases=['connect'])
    async def join(self, ctx: commands.Context, *, channel: nextcord.TextChannel = None) -> None:
        
        if not channel:
            channel = getattr(ctx.author.voice, 'channel', None)
            if not channel:
                raise commands.CheckFailure('You must be in a voice channel to use this command'
                                            'without specifying the channel argument.')

        
        await ctx.author.voice.channel.connect(cls=pomice.Player)
        await ctx.send(f'Joined the voice channel `{channel}`')
        
    @commands.command(name='play')
    async def play(self, ctx, *, search: str) -> None:
        
        if not ctx.voice_client:
            await ctx.invoke(self.join) 

        player = ctx.voice_client        

        results = await player.get_tracks(query=f'{search}')
        
        if not results:
            raise commands.CommandError('No results were found for that search term.')
        
        if isinstance(results, pomice.Playlist):
            await player.play(track=results.tracks[0])
        else:
            await player.play(track=results[0])

            
bot = MyBot()
bot.run("token here")
 ```

# FAQ
Why is it saying "Cannot connect to host"?

- You need to have a Lavalink node setup before you can use this library. Download it [here](https://github.com/freyacodes/Lavalink/releases/tag/3.3.2.5)

What experience do I need?

- This library requires that you have some experience with Python, asynchronous programming and the nextcord library.

Why is it saying "No module named pomice found"?

- You need to [install](#Install) the package before you can use it 

# Contributors

- Thanks to [vveeps](https://github.com/vveeps) for implementing some features I wasn't able to do myself

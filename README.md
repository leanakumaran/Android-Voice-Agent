# Jarvis Voice AI Assisant
A smart, professional–yet–fun voice assistant powered by LiveKit Agents, with support for both OpenAI Realtime API and Google Gemini Live API.

🚀 Installation

Install required packages using uv:

uv add \
  "livekit-agents" \
  "livekit-plugins-noise-cancellation~=0.2" \
  "python-dotenv"

🔑 Environment Variables

Create a .env.local file in your project root and add:

GOOGLE_API_KEY=<Your Google API Key>
LIVEKIT_API_KEY=<Your LiveKit API Key>
LIVEKIT_API_SECRET=<Your LiveKit API Secret>
LIVEKIT_URL=wss://jarvis-8fgf6ibk.livekit.cloud

🐍 Agent Code (Python)
OpenAI Realtime API Example
from dotenv import load_dotenv

from livekit import agents
from livekit.agents import AgentSession, Agent, RoomInputOptions
from livekit.plugins import (
    openai,
    noise_cancellation,
)

load_dotenv(".env.local")


class Assistant(Agent):
    def __init__(self) -> None:
        super().__init__(instructions="You are a helpful voice AI assistant.")


async def entrypoint(ctx: agents.JobContext):
    session = AgentSession(
        llm=openai.realtime.RealtimeModel(
            voice="coral"
        )
    )

    await session.start(
        room=ctx.room,
        agent=Assistant(),
        room_input_options=RoomInputOptions(
            noise_cancellation=noise_cancellation.BVC(),
        ),
    )

    await session.generate_reply(
        instructions="Greet the user and offer your assistance. You should start by speaking in English."
    )


if __name__ == "__main__":
    agents.cli.run_app(agents.WorkerOptions(entrypoint_fnc=entrypoint))

Google Gemini Live API Example
from livekit.plugins import google

session = AgentSession(
    llm=google.beta.realtime.RealtimeModel(
        model="gemini-2.0-flash-exp",
        voice="Puck",
        temperature=0.8,
        instructions="You are a helpful assistant",
    ),
)

📥 Download Model Files
uv run agent.py download-files

🗣️ Speak to Your Agent

Run the console to interact with your agent via terminal:

uv run agent.py console

#🌍 Connect to Playground

Start your agent in dev mode to connect it to LiveKit and make it accessible from anywhere:

uv run agent.py dev

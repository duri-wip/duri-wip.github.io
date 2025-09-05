---
layout: post
title: "FastMCP 기초 사용법"
excerpt: "FastMCP를 활용한 MCP 서버 구축하기"
date: 2025-08-07
categories: [ML&AI]
subcategory: genai-llm
tags: [genai-llm, mcp, fastmcp, fastapi, tools]
---

from fastapi import FastAPI
from fastmcp import FastMCP
from typing import Dict

app = FastAPI()
mcp = FastMCP(
name="Tool Server",
version="1.0.0"
)

@mcp.tool
def plus(a: int, b: int) -> int:
"""
두 숫자를 더하는 도구입니다.

    Args:
        a: 첫 번째 숫자
        b: 두 번째 숫자

    Returns:
        두 숫자의 합

    Examples:
        - a=5, b=3 -> 8
    """
    return a + b

@app.get("/tools")
async def get_tools(): # await 사용
tools = await mcp.get_tools()
tools_info = {}

    for name, tool in tools.items():
        tools_info[name] = {
            "name": name,
            "description": tool.description,
            "enabled": tool.enabled,
            "metadata": {
                "parameters": {
                    "a": {"type": "integer", "description": "첫 번째 숫자"},
                    "b": {"type": "integer", "description": "두 번째 숫자"}
                },
                "returns": {"type": "integer", "description": "두 숫자의 합"},
                "examples": ["a=5, b=3 -> 8"]
            }
        }

    return tools_info

@app.get("/tools/{tool_name}")
async def get_tool(tool_name: str): # await 사용
tools = await mcp.get_tools()
if tool_name not in tools:
return {"error": "Tool not found"}

    tool = tools[tool_name]
    return {
        "name": tool_name,
        "description": tool.description,
        "enabled": tool.enabled,
        "metadata": {
            "parameters": {
                "a": {"type": "integer", "description": "첫 번째 숫자"},
                "b": {"type": "integer", "description": "두 번째 숫자"}
            },
            "returns": {"type": "integer", "description": "두 숫자의 합"},
            "examples": ["a=5, b=3 -> 8"]
        }
    }

if **name** == "**main**":
import uvicorn
uvicorn.run(app, host="0.0.0.0", port=3000)

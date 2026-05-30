# Software Design Description (SDD)
## Project Title: Multi-Tenant AI Voice Ordering Platform & Real-Time Restaurant Administration Dashboard

**Author:** Karan Kadvani  
**Role:** AI / Full-Stack Engineering Intern  
**Date:** May 2026  

---

## 1. System Overview & Executive Summary

### 1.1 Objective
This system automates the restaurant telephone ordering pipeline by using a voice-based generative AI agent. The system answers inbound audio streams, transcribes user requests in real time, interprets intent using an LLM equipped with dynamic tool calling, tracks live order completeness states, saves structured records to a database, and reflects all data instantly onto a React-based administration dashboard.

### 1.2 Core Capabilities
* **Volatile State Monitoring:** Tracks whether an order is INCOMPLETE (user is still adding items or checking prices) or COMPLETE (user has explicitly confirmed and wrapped up the order).
* **Contextual Knowledge Engine:** Empowers the AI agent to access menu items, descriptions, and up-to-the-minute pricing from attached system documents/databases to compute totals live.
* **Real-Time Admin Sync:** Allows authenticated restaurant managers to view orders pop onto their screen instantly via WebSocket connections without forcing manual page updates.

---

## 2. System Topography & Data Flow

This architectural diagram illustrates how raw audio input transforms into a structured data payload, updates the system state, and notifies the restaurant administration screen.

```text
  [ Customer (Voice) ] 
           │
           │ (Bi-directional Audio Stream) 
           ▼
    [ Voice Gateway ] <─── Handles VAD (Voice Activity Detection) & Streaming 
           │
           │ (STT Transcription) 
           ▼
[ LLM Orchestration ] <─── References Menu Docs & Evaluates Completeness 
           │
           │ (Structured JSON Tool Call) 
           ▼
   [ FastAPI Backend ] <─── Routes core logic and updates session states 
     /           \
    /             \
   / (SQL Writes)  \ (WS Broadcast Payload) 
  v                 v
[ PostgreSQL DB ]  [ WebSocket Instance ] 
                            │
                            │ (Event: ORDER_STREAMED) 
                            v
                   [ React Admin Panel ]

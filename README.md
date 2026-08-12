# WRO Future Engineers 2026 - Autonomous Robot Platform

## Executive Summary
This repository documents the high-level system architecture, hardware control pipeline, micro-ROS communication layer, and active-low signal conditioning circuit for our WRO competition robot.

The system utilizes a **Raspberry Pi 4** running ROS 2 as the primary compute node and an **STM32F446RE** microcontroller running micro-ROS for real-time motor control and signal generation.

---

## 1. System Block Diagram

```mermaid
graph TD
    subgraph High_Level ["High-Level Control (Raspberry Pi 4)"]
        ROS[ROS 2 Workspace Nodes] -->|Publish Topics| Agent[Micro-ROS Agent]
    end

    subgraph Transport ["Communication Layer"]
        Agent <-->|USB / Serial UART @ 115200 baud| Client[Micro-ROS Client on STM32]
    end

    subgraph Low_Level ["Low-Level Control (STM32F446RE)"]
        Client --> MCU[STM32 PWM Signal Generator]
        MCU -->|TIM2 PWM - PA0 & PA1| LevelShifter[NPN Transistor Level-Shifter]
        MCU -->|TIM3 PWM - PA6| Servo[DC Servo Motor]
    end

    subgraph Actuation ["Power & Drive System"]
        LevelShifter -->|Inverted 5V PWM| Driver[BTS7960 H-Bridge Driver]
        P12V[12V Power Supply] --> Driver
        Driver --> Motor[12V DC Motor]
    end

# SkillSwap-DAO Contract

## Overview

The SkillSwap contract is a TypeScript-based implementation of a decentralized skill exchange platform. Users can register, post skill exchange requests, accept offers, complete exchanges, and rate participants. This implementation uses the plu-ts library for contract management in TypeScript.

## Features

User Registration: Allows users to register with a username.
Posting Skill Exchanges: Users can post skills they offer and request skills they need.
Accepting Exchanges: Other users can accept posted skill exchanges.
Completing Exchanges: Participants can mark an exchange as completed.
Rating Users: Participants can rate each other after an exchange is completed.

## Installation

To use this code, you need to have a TypeScript environment set up with the plu-ts library. Follow these steps to set up your project:

### Install Dependencies

`npm install plu-ts @types/node`

### Add the Code

Create a file named SkillSwap.ts in your TypeScript project and copy the contract code into this file.

### Compile TypeScript

Make sure you have TypeScript installed and configured. Compile your TypeScript files using:

`npx tsc`

### Contract Usage

### Register User

`await skillSwap.registerUser(userName: string): Promise<void>`

### Post Skill Exchange

`await skillSwap.postSkillExchange(skillOffered: string, skillRequested: string): Promise<void>`

### Accept Skill Exchange

`await skillSwap.acceptSkillExchange(id: BigNumber): Promise<void>`

### Complete Skill Exchange

`await skillSwap.completeSkillExchange(id: BigNumber): Promise<void>`

### Rate User

`await skillSwap.rateUser(id: BigNumber, rating: BigNumber): Promise<void>`








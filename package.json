import { Contract, Event, BigNumber } from 'plu-ts';

export class SkillSwap extends Contract {
  private exchangeCounter: BigNumber = BigNumber.from(0);

  private users: Map<string, User> = new Map();
  private skillExchanges: Map<BigNumber, SkillExchange> = new Map();
  private userExchanges: Map<string, BigNumber[]> = new Map();

  // Events
  public readonly UserRegistered = new Event();
  public readonly SkillExchangePosted = new Event();
  public readonly SkillExchangeAccepted = new Event();
  public readonly SkillExchangeCompleted = new Event();
  public readonly UserRated = new Event();

  // Register User
  public async registerUser(userName: string): Promise<void> {
    const userAddress = this.sender;

    if (this.users.has(userAddress)) {
      throw new Error('User already registered');
    }

    this.users.set(userAddress, {
      userAddress,
      userName,
      reputation: BigNumber.from(0),
      ratingCount: BigNumber.from(0)
    });

    this.UserRegistered.emit(userAddress, userName);
  }

  // Post Skill Exchange
  public async postSkillExchange(skillOffered: string, skillRequested: string): Promise<void> {
    const userAddress = this.sender;

    if (!this.users.has(userAddress)) {
      throw new Error('User not registered');
    }

    this.exchangeCounter = this.exchangeCounter.add(1);

    const id = this.exchangeCounter;

    this.skillExchanges.set(id, {
      id,
      requester: userAddress,
      skillOffered,
      skillRequested,
      offererReputation: BigNumber.from(0),
      offerer: '0x0000000000000000000000000000000000000000',
      isCompleted: false,
      ratingFromRequester: BigNumber.from(0),
      ratingFromOfferer: BigNumber.from(0)
    });

    const exchanges = this.userExchanges.get(userAddress) || [];
    exchanges.push(id);
    this.userExchanges.set(userAddress, exchanges);

    this.SkillExchangePosted.emit(id, userAddress, skillOffered, skillRequested);
  }

  // Accept Skill Exchange
  public async acceptSkillExchange(id: BigNumber): Promise<void> {
    const exchange = this.skillExchanges.get(id);

    if (!exchange) {
      throw new Error('Skill exchange not found');
    }

    if (exchange.offerer !== '0x0000000000000000000000000000000000000000') {
      throw new Error('Skill exchange already accepted');
    }

    if (exchange.requester === this.sender) {
      throw new Error('Requester cannot accept own request');
    }

    if (!this.users.has(this.sender)) {
      throw new Error('User not registered');
    }

    exchange.offerer = this.sender;
    exchange.offererReputation = this.users.get(this.sender)!.reputation;

    const exchanges = this.userExchanges.get(this.sender) || [];
    exchanges.push(id);
    this.userExchanges.set(this.sender, exchanges);

    this.SkillExchangeAccepted.emit(id, this.sender);
  }

  // Complete Skill Exchange
  public async completeSkillExchange(id: BigNumber): Promise<void> {
    const exchange = this.skillExchanges.get(id);

    if (!exchange) {
      throw new Error('Skill exchange not found');
    }

    if (exchange.isCompleted) {
      throw new Error('Skill exchange already completed');
    }

    if (exchange.requester !== this.sender && exchange.offerer !== this.sender) {
      throw new Error('Only participants can complete');
    }

    exchange.isCompleted = true;

    this.SkillExchangeCompleted.emit(id);
  }

  // Rate User
  public async rateUser(id: BigNumber, rating: BigNumber): Promise<void> {
    if (rating.lt(BigNumber.from(1)) || rating.gt(BigNumber.from(5))) {
      throw new Error('Rating must be between 1 and 5');
    }

    const exchange = this.skillExchanges.get(id);

    if (!exchange) {
      throw new Error('Skill exchange not found');
    }

    if (!exchange.isCompleted) {
      throw new Error('Skill exchange not completed');
    }

    const user = this.users.get(this.sender);

    if (!user) {
      throw new Error('User not registered');
    }

    if (this.sender === exchange.requester) {
      if (!exchange.ratingFromRequester.eq(BigNumber.from(0))) {
        throw new Error('Requester already rated');
      }
      exchange.ratingFromRequester = rating;

      const offerer = this.users.get(exchange.offerer)!;
      offerer.reputation = this.calculateNewReputation(offerer.reputation, offerer.ratingCount, rating);
      offerer.ratingCount = offerer.ratingCount.add(1);
      this.UserRated.emit(exchange.offerer, offerer.reputation);
    } else if (this.sender === exchange.offerer) {
      if (!exchange.ratingFromOfferer.eq(BigNumber.from(0))) {
        throw new Error('Offerer already rated');
      }
      exchange.ratingFromOfferer = rating;

      const requester = this.users.get(exchange.requester)!;
      requester.reputation = this.calculateNewReputation(requester.reputation, requester.ratingCount, rating);
      requester.ratingCount = requester.ratingCount.add(1);
      this.UserRated.emit(exchange.requester, requester.reputation);
    } else {
      throw new Error('Only participants can rate');
    }
  }

  private calculateNewReputation(currentReputation: BigNumber, ratingCount: BigNumber, newRating: BigNumber): BigNumber {
    return currentReputation.mul(ratingCount).add(newRating).div(ratingCount.add(1));
  }
}

// Define types for the contract
interface User {
  userAddress: string;
  userName: string;
  reputation: BigNumber;
  ratingCount: BigNumber;
}

interface SkillExchange {
  id: BigNumber;
  requester: string;
  skillOffered: string;
  skillRequested: string;
  offererReputation: BigNumber;
  offerer: string;
  isCompleted: boolean;
  ratingFromRequester: BigNumber;
  ratingFromOfferer: BigNumber;
}

# Staking Guide

Learn how to stake ORX tokens and earn passive rewards on OracleX.

## 🎯 What is Staking?

Staking allows you to lock up ORX tokens for a fixed period to earn rewards. The longer you lock, the higher your Annual Percentage Yield (APY).

**Benefits:**
- 💰 Earn passive income (5-72% APY)
- 🗳️ Increased voting power in governance
- 🎖️ Build reputation and unlock achievements
- 🔒 Contribute to platform security

**Requirements:**
- Minimum: 100 ORX tokens
- Wallet with BNB for gas fees
- Choose lock period: 30, 90, 180, or 365 days

## 📊 Staking Pools

### Available Lock Periods

| Lock Period | Base APY | Multiplier | Effective APY | Min Stake |
|-------------|----------|------------|---------------|-----------|
| **30 days** | 5% | 1.0x | **5%** | 100 ORX |
| **90 days** | 12% | 1.2x | **14.4%** | 100 ORX |
| **180 days** | 25% | 1.5x | **37.5%** | 100 ORX |
| **365 days** | 40% | 1.8x | **72%** | 100 ORX |

### APY Breakdown

The effective APY includes:
1. **Base Rewards**: Fixed rate for lock period
2. **Time Multiplier**: Bonus for longer locks
3. **Volume Bonus**: Extra rewards from platform fees

```typescript
// APY Calculation Example
const calculateAPY = (baseAPY: number, lockDays: number) => {
  // Get multiplier based on lock period
  const multipliers = {
    30: 1.0,
    90: 1.2,
    180: 1.5,
    365: 1.8
  };
  
  const multiplier = multipliers[lockDays] || 1.0;
  const effectiveAPY = baseAPY * multiplier;
  
  return effectiveAPY;
};

// Example: 90-day lock
const baseAPY = 0.12; // 12%
const multiplier = 1.2;
const effectiveAPY = 0.12 * 1.2; // 14.4%
```

## 💰 How Much Can You Earn?

### Reward Calculations

```typescript
// Simple reward formula
Reward = (Staked Amount × APY × Lock Period) / 365 days

// Examples:

// 30-day stake
Amount: 1,000 ORX
APY: 5%
Period: 30 days
Reward: (1000 × 0.05 × 30) / 365 = 4.11 ORX

// 90-day stake
Amount: 5,000 ORX
APY: 14.4%
Period: 90 days
Reward: (5000 × 0.144 × 90) / 365 = 177.53 ORX

// 180-day stake
Amount: 10,000 ORX
APY: 37.5%
Period: 180 days
Reward: (10000 × 0.375 × 180) / 365 = 1,849.32 ORX

// 365-day stake
Amount: 20,000 ORX
APY: 72%
Period: 365 days
Reward: 20000 × 0.72 = 14,400 ORX
```

### ROI Examples

| Stake Amount | Lock Period | APY | Rewards | Total Return | ROI |
|--------------|-------------|-----|---------|--------------|-----|
| 1,000 ORX | 30 days | 5% | 4 ORX | 1,004 ORX | 0.4% |
| 5,000 ORX | 90 days | 14.4% | 178 ORX | 5,178 ORX | 3.6% |
| 10,000 ORX | 180 days | 37.5% | 1,849 ORX | 11,849 ORX | 18.5% |
| 20,000 ORX | 365 days | 72% | 14,400 ORX | 34,400 ORX | 72% |

## 🚀 How to Stake

### Step 1: Navigate to Staking Page

1. Go to https://oraclex.com/staking
2. Connect your wallet
3. Ensure you're on BNB Chain Testnet

### Step 2: Check Your ORX Balance

```typescript
// Your available balance is displayed at top
Available Balance: 10,000 ORX
Staked Balance: 0 ORX
Total Balance: 10,000 ORX
```

**Don't have ORX?** [Get ORX from faucet →](getting-orx.md)

### Step 3: Choose Stake Amount

Enter the amount you want to stake:

**Considerations:**
- Minimum: 100 ORX
- Maximum: Your available balance
- Keep some ORX for predictions and gas fees

```typescript
// Recommended allocation
Total ORX: 10,000

Staking: 7,000 ORX (70%)
Predictions: 2,000 ORX (20%)
Reserve: 1,000 ORX (10%)
```

### Step 4: Select Lock Period

Choose based on your goals:

**Short-term (30 days)**
- ✅ Quick returns
- ✅ High flexibility
- ⚠️ Lower APY
- Best for: Testing staking

**Medium-term (90 days)**
- ✅ Good APY (14.4%)
- ✅ Reasonable lock time
- ✅ Balanced risk/reward
- Best for: Regular stakers

**Long-term (180 days)**
- ✅ High APY (37.5%)
- ✅ Significant rewards
- ⚠️ Longer commitment
- Best for: Patient investors

**Maximum (365 days)**
- ✅ Highest APY (72%)
- ✅ Maximum rewards
- ⚠️ Full year lock
- Best for: Long-term believers

### Step 5: Review & Confirm

Before staking, review:

```typescript
Staking Summary:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Amount: 5,000 ORX
Lock Period: 90 days
APY: 14.4%
Expected Rewards: 177.53 ORX
Total Return: 5,177.53 ORX
Unlock Date: February 8, 2026
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

⚠️ Early withdrawal penalty: 50% of rewards
⚠️ Tokens locked until unlock date
✅ Claim rewards anytime after unlock
```

### Step 6: Approve ORX (First Time Only)

If staking for the first time:

**Transaction 1: Approve**
1. MetaMask popup appears
2. Approving ORX spending
3. Click "Confirm"
4. Wait ~3 seconds
5. Gas cost: ~$0.02

```typescript
// Approval transaction
Function: approve(address spender, uint256 amount)
Spender: Staking Contract (0x007Aaa...)
Amount: Your stake amount or unlimited
Gas: ~50,000
```

**💡 Pro Tip:** Approve unlimited amount to avoid approving again for future stakes.

### Step 7: Stake Tokens

**Transaction 2: Stake**
1. Second MetaMask popup
2. Staking transaction
3. Click "Confirm"
4. Wait ~3 seconds
5. Gas cost: ~$0.04

```typescript
// Staking transaction
Function: stake(uint256 amount, uint256 lockPeriod)
Amount: 5,000 ORX (in wei)
Lock Period: 7776000 seconds (90 days)
Gas: ~100,000
```

### Step 8: Confirmation

Success! Your stake is now active:
- ✅ Transaction confirmed
- ✅ Tokens locked in contract
- ✅ Rewards accumulating
- ✅ Visible in "My Stakes" section

## 📊 Managing Your Stakes

### View Active Stakes

Navigate to **Staking Dashboard** to see:

```typescript
My Stakes
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Stake #1
Amount: 5,000 ORX
Lock Period: 90 days
APY: 14.4%
Staked On: Nov 10, 2025
Unlock Date: Feb 8, 2026
Time Remaining: 90 days
Earned Rewards: 0.49 ORX (updating daily)
Status: 🔒 Locked

Stake #2
Amount: 2,000 ORX
Lock Period: 30 days
APY: 5%
Staked On: Oct 10, 2025
Unlock Date: Nov 9, 2025
Time Remaining: 0 days
Earned Rewards: 2.74 ORX
Status: ✅ Unlocked - Ready to Claim!
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Real-Time Rewards Tracking

Watch your rewards accumulate:

```typescript
// Rewards update every block (~3 seconds)
Current Rewards: 0.49 ORX
Projected Final: 177.53 ORX
Progress: 1% (1/90 days)

// Daily accrual
Daily Rewards: 1.97 ORX/day
Weekly Rewards: 13.79 ORX/week
Monthly Rewards: 59.18 ORX/month
```

### Stake Details

Click any stake to view:
- Stake amount
- Lock period
- Start/end dates
- APY rate
- Earned rewards
- Transaction hash
- Contract address

## 💎 Claiming Rewards

### When Lock Period Ends

After your lock period expires:

1. Stake status changes to **"Unlocked"**
2. **"Claim"** button appears
3. You can claim anytime (no rush)
4. Rewards continue earning until claimed

### Claiming Process

**Step 1: Navigate to Claims**
1. Go to Staking page
2. Find unlocked stake
3. Click **"Claim Rewards"**

**Step 2: Confirm Transaction**
```typescript
Claim Summary:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Original Stake: 5,000 ORX
Earned Rewards: 177.53 ORX
Total Claim: 5,177.53 ORX
Gas Fee: ~$0.03
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**Step 3: Sign Transaction**
1. MetaMask popup
2. Click "Confirm"
3. Wait for confirmation
4. Tokens sent to wallet

**Step 4: Success**
- ✅ Stake + rewards in wallet
- ✅ Available for predictions or restaking
- ✅ Transaction complete

## ⚠️ Early Withdrawal

### Penalty for Early Exit

If you withdraw before lock period ends:

```typescript
Penalty: 50% of earned rewards

Example:
Stake: 5,000 ORX
Lock: 90 days
Exit after: 45 days (halfway)
Earned so far: 88.76 ORX
Penalty: 44.38 ORX (50%)
You receive: 5,044.38 ORX
Lost rewards: 44.38 ORX
```

### When to Consider Early Withdrawal

**Good Reasons:**
- Emergency need for funds
- Better opportunity elsewhere
- Market conditions changed drastically

**Bad Reasons:**
- Impatience
- Minor price movements
- FOMO on other investments

### How to Withdraw Early

1. Find your stake
2. Click **"Emergency Unstake"**
3. Review penalty warning
4. Confirm if certain
5. Receive stake minus penalty

**⚠️ Warning:** This action cannot be undone!

## 📈 Staking Strategies

### 1. Ladder Strategy

Spread stakes across multiple lock periods:

```typescript
Total: 10,000 ORX

Stake 1: 2,500 ORX × 30 days = 3.42 ORX
Stake 2: 2,500 ORX × 90 days = 88.76 ORX
Stake 3: 2,500 ORX × 180 days = 462.33 ORX
Stake 4: 2,500 ORX × 365 days = 1,800 ORX

Benefits:
- Regular unlock schedule
- Access to funds periodically
- Diversified risk
- Average APY: ~24%
```

### 2. All-In Long-Term

Maximum returns strategy:

```typescript
Total: 10,000 ORX × 365 days @ 72% APY
Rewards: 7,200 ORX
Final: 17,200 ORX after 1 year

Benefits:
- Highest possible returns
- Simple management
- Maximum voting power

Risks:
- All funds locked 1 year
- No flexibility
- Opportunity cost
```

### 3. Auto-Compound

Reinvest rewards immediately:

```typescript
Cycle 1: Stake 5,000 ORX × 90 days
Unlock: Receive 5,177.53 ORX

Cycle 2: Stake 5,177.53 ORX × 90 days
Unlock: Receive 5,364.05 ORX

Cycle 3: Stake 5,364.05 ORX × 90 days
Unlock: Receive 5,559.96 ORX

After 1 year: ~5,900 ORX (18% compound APY)
```

### 4. Hybrid Approach

Balance staking with active predictions:

```typescript
Total Portfolio: 10,000 ORX

Staking (70%): 7,000 ORX
- 365-day stake for high APY
- Passive income source
- Voting power

Active Trading (30%): 3,000 ORX
- Make predictions
- Capture opportunities
- Stay liquid

Expected Annual Return:
Staking: 7,000 × 0.72 = 5,040 ORX
Predictions: 3,000 × 0.30 = 900 ORX (est.)
Total: 5,940 ORX (59.4% combined return)
```

## 🎮 Staking Achievements

### Unlock Badges

Earn achievements through staking:

- 🥉 **First Stake**: Complete first stake
- 🏦 **Diamond Hands**: Stake 10,000+ ORX
- ⏰ **Long-Term Believer**: Complete 365-day stake
- 🔄 **Serial Staker**: 10 completed stakes
- 💎 **Whale Staker**: Stake 100,000+ ORX
- 👑 **Staking Legend**: 1M+ ORX total staked

### Level Up

Staking contributes to your level:
- Stake ORX: +5 XP per 100 ORX
- Complete stake: +50 XP
- Long-term stakes: +bonus XP

### Leaderboards

Compete on staking leaderboards:
- **Most Staked**: Total amount
- **Longest Lock**: Duration champions
- **Highest Rewards**: Earnings leaders
- **Most Stakes**: Volume leaders

## 📊 Staking Analytics

### Track Performance

Monitor your staking performance:

```typescript
Staking Dashboard
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Total Staked: 15,000 ORX
Total Rewards Earned: 2,500 ORX
Active Stakes: 3
Completed Stakes: 7
Average APY: 28%
Total ROI: 16.67%
Voting Power: 18,000
Rank: #145 / 5,000 stakers
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Historical Data

View past stakes:
- Stake dates
- Lock periods
- Rewards earned
- APY achieved
- Transaction hashes

## 🗳️ Governance Benefits

### Increased Voting Power

Staked ORX gives you more governance influence:

```typescript
Voting Power = Staked Amount × Time Multiplier

Examples:
1,000 ORX staked 30 days = 1,083 voting power
1,000 ORX staked 90 days = 1,333 voting power
1,000 ORX staked 180 days = 1,667 voting power
1,000 ORX staked 365 days = 2,000 voting power

Longer stakes = More governance influence
```

### Proposal Creation

High stakers can create proposals:
- Minimum: 10,000 ORX staked
- Propose protocol changes
- Shape platform future

### Vote on Proposals

Participate in governance:
- Vote with staked ORX
- Delegate voting power
- Earn reputation for participation

## 🔒 Security & Safety

### Smart Contract Security

Staking contract features:
- ✅ Audited by CertiK
- ✅ Time-locked withdrawals
- ✅ No admin keys
- ✅ Immutable logic
- ✅ Non-custodial (you control keys)

### Best Practices

**✅ DO:**
- Start with small amounts
- Understand lock periods
- Keep seed phrase safe
- Diversify lock periods
- Monitor rewards regularly

**❌ DON'T:**
- Stake more than you can afford to lock
- Share your private keys
- Use exchange wallets for staking
- Ignore security warnings
- Rush transactions

## 🔧 Troubleshooting

### Common Issues

**"Insufficient Balance"**
- Ensure you have enough ORX
- Keep BNB for gas fees
- Check minimum stake (100 ORX)

**"Approval Failed"**
- Increase gas limit
- Check network connection
- Retry transaction

**"Transaction Failed"**
- Verify correct network (BSC Testnet)
- Ensure sufficient gas
- Check contract status

**"Can't Claim Rewards"**
- Verify lock period ended
- Check unlock date
- Refresh page

## 📱 Mobile Staking

Stake from mobile using MetaMask app:

1. Open MetaMask mobile app
2. Visit oraclex.com in DApp browser
3. Connect wallet
4. Navigate to Staking
5. Follow same steps as desktop

**Mobile Tips:**
- Ensure stable internet
- Double-check amounts
- Save transaction hashes
- Enable notifications

## 🔗 Resources

- **Stake Now**: https://oraclex.com/staking
- **Staking Contract**: https://testnet.bscscan.com/address/0x007Aaa...
- **APY Calculator**: https://oraclex.com/calculator
- **Support**: https://discord.gg/oraclex

## Next Steps

Now that you know how to stake:

1. ✅ [Participate in Governance →](governance.md)
2. ✅ [View Your Rewards →](https://oraclex.com/staking)
3. ✅ [Join Staking Community →](https://discord.gg/oraclex)

---

<div style="background: linear-gradient(135deg, #FFD700, #9333EA); padding: 1.5rem; border-radius: 12px; color: white;">
  <strong>💎 Start Earning Today!</strong> Stake your ORX tokens now and earn up to 72% APY while supporting the OracleX ecosystem.
</div>

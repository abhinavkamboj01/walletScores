# Wallet Scoring 

1. Data Collection Method
   Since real-time on-chain data access was not enabled for this task, we simulated transaction history for 100 Ethereum wallets provided. However, in a              production-grade pipeline, data collection would involve:

   Data Sources:

     Compound V2/V3 smart contracts for borrow, repay, liquidation events.

     Etherscan API, Alchemy, or The Graph for efficient on-chain querying.

   Methods:

     Extract user-specific events from Comptroller, cTokens, and Comet contracts.

     Derive wallet behaviors by parsing logs and decoding input data.

2. Feature Selection Rationale
   We selected features that capture historical, behavioral, and financial risk indicators. Each reflects either default risk, volatility, or usage                   discipline.

  Feature	                   Description	                                                           Risk Implication
  repayment_ratio	           Total repaid / Total borrowed	                                         Low ratio implies higher credit risk
  num_liquidations	         How often wallet was liquidated	                                       High count indicates poor risk management
  wallet_age_days	           Time since the wallet's first tx	                                       Older wallets are more trustworthy
  collateral_ratio	         Avg. ratio of supplied collateral to borrowed amount	                   Higher = better buffer against liquidation
  tx_frequency	             Count of DeFi interactions over time	                                   Indicates engagement & transparency



3. Scoring Method
  We designed a normalized, weighted scoring model that produces a risk score from 0 (risky) to 1000 (safe).

  Steps:

  Feature Normalization:

    Used MinMaxScaler on risk-inducing features to bring them to a common [0–1] scale.

    Applied inversions where appropriate (e.g., higher liquidations = worse).

  Weighting Strategy:
  
  | Metric                | Weight |
  | --------------------- | ------ |
  | Repayment Ratio       | 30%    |
  | Liquidation Count     | 25%    |
  | Wallet Age            | 15%    |
  | Collateral Ratio      | 20%    |
  | Transaction Frequency | 10%    |


  Final Score Formula:

    score = (1 - weighted_normalized_risk) * 1000


4. Justification of Risk Indicators
 
  Each feature aligns with core risk insights used in traditional and decentralized credit evaluation:

    Repayment Ratio is the most direct measure of creditworthiness.
  
    Liquidation Count reflects operational risk or misuse of leverage.

    Wallet Age acts as a proxy for history and behavioral data.

    Collateral Ratio protects the protocol from market volatility.

    Tx Frequency reflects reliability, engagement, and accountability.



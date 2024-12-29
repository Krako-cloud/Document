# KrakoLabs document List


#### 1. ARCHITECTURE DIAGRAMS 

  1. High-Level System Architecture Diagram

  1. Detailed Architecture of MOTHER

  1. Data Flow Diagram

  1. Deployment Diagram


#### 2. USER JOURNEYS 
  1. End User Journey - Contributing Idle Computing Power

  1. Developer Journey - Integrating KrakenNet API/SDK

  1. System Interaction Overview


#### 3. Detailed Financial Models 
    import pandas as pd
    import numpy as np
    
    def calculate_token_emissions(total_supply=1_000_000_000, months=48):
        """Calculate monthly token emissions and financial metrics"""
        
        # Token allocation and prices
        allocations = {
            'seed': {'percentage': 0.05, 'price': 0.04, 'lockup': 6, 'vesting': 18},
            'strategic': {'percentage': 0.10, 'price': 0.06, 'lockup': 3, 'vesting': 15},
            'public': {'percentage': 0.14, 'price': 0.07, 'lockup': 0, 'vesting': 12},
            'ido': {'percentage': 0.06, 'price': 0.08, 'lockup': 0, 'vesting': 3},
            'network_rewards': {'percentage': 0.20, 'vesting': 48},
            'developer_incentives': {'percentage': 0.10, 'vesting': 36},
            'team_advisors': {'percentage': 0.20, 'lockup': 12, 'vesting': 36},
            'operations': {'percentage': 0.10, 'vesting': 48},
            'reserve': {'percentage': 0.05, 'lockup': 24}
        }
        
        # Initialize monthly emission dataframe
        df = pd.DataFrame(index=range(months))
        
        # Calculate emissions for each allocation
        for category, params in allocations.items():
            tokens = total_supply * params['percentage']
            lockup = params.get('lockup', 0)
            vesting = params.get('vesting', 0)
            
            if category == 'ido':
                # Special case for IDO with 50% upfront
                initial_release = tokens * 0.5
                remaining = tokens * 0.5
                monthly = remaining / vesting
                emissions = [0] * lockup + [initial_release] + [monthly] * (vesting - 1)
            else:
                monthly = tokens / vesting if vesting > 0 else 0
                emissions = [0] * lockup + [monthly] * (vesting if vesting > 0 else 1)
            
            df[f'{category}_emissions'] = emissions[:months] + [0] * (months - len(emissions))
        
        # Calculate cumulative metrics
        df['monthly_emissions'] = df.sum(axis=1)
        df['cumulative_supply'] = df['monthly_emissions'].cumsum()
        
        return df

    def project_network_metrics(months=48, initial_users=6000, initial_models=270000):
        """Project network growth and usage metrics"""
        
        # Growth assumptions
        monthly_user_growth = 0.40  # 40% monthly growth
        monthly_model_growth = 0.35  # 35% monthly model growth
        compute_cost_savings = 0.90  # 90% cost savings vs traditional
        
        # Initialize projections dataframe
        df = pd.DataFrame(index=range(months))
        
        # Calculate user and model growth
        df['active_users'] = [initial_users * (1 + monthly_user_growth) ** i for i in range(months)]
        df['models_processed'] = [initial_models * (1 + monthly_model_growth) ** i for i in range(months)]
        
        # Calculate compute metrics
        avg_compute_cost_per_model = 5  # USD
        df['traditional_compute_costs'] = df['models_processed'] * avg_compute_cost_per_model
        df['kraken_compute_costs'] = df['traditional_compute_costs'] * (1 - compute_cost_savings)
        df['cost_savings'] = df['traditional_compute_costs'] - df['kraken_compute_costs']
        
        # Calculate network revenue
        take_rate = 0.20  # 20% platform fee
        df['network_revenue'] = df['kraken_compute_costs'] * take_rate
        
        return df

    def calculate_token_metrics(total_supply=1_000_000_000, initial_price=0.08):
        """Calculate key token metrics"""
        
        emissions_df = calculate_token_emissions(total_supply)
        network_df = project_network_metrics()
        
        # Combine metrics
        df = pd.concat([emissions_df, network_df], axis=1)
        
        # Calculate token metrics
        df['token_price'] = initial_price  # Simplified - would need market dynamics model
        df['market_cap'] = df['cumulative_supply'] * df['token_price']
        df['fully_diluted_valuation'] = total_supply * df['token_price']
        
        return df

    # Generate projections
    projections = calculate_token_metrics()
    
    # Print key metrics for first 12 months
    print("\nMonthly Projections (First Year):")
    print(projections.head(12)[['active_users', 'models_processed', 'network_revenue', 'market_cap']])
    
    # Calculate key statistics
    initial_circulating = projections['cumulative_supply'][0]
    initial_market_cap = initial_circulating * 0.08  # Initial token price
    total_raise = (50_000_000 * 0.04) + (100_000_000 * 0.06) + (140_000_000 * 0.07) + (60_000_000 * 0.08)
    
    print("\nKey Token Metrics:")
    print(f"Initial Circulating Supply: {initial_circulating:,.0f} $INK")
    print(f"Initial Market Cap: ${initial_market_cap:,.2f}")
    print(f"Total Raise: ${total_raise:,.2f}")
   
 
#### 4. Governance Mechanisms

##### 1. Governance Structure
  ######   Voting Power
        - $INK = 1 base vote
        - Voting power multipliers:
        - Staked tokens: 1.5x
        - Network node operators: 2x
        - Active developers: 2x
        - Long-term holders (>6 months): 1.25x

  #####   Governance Bodies
  ######     1. Token Holders
        - All $INK holders
        - Basic voting rights
        - Proposal submission (requires 1M $INK)
  ###### 2. Technical Committee
      - 7 members
      - Elected by token holders
      - Focus: Technical proposals & upgrades
      - Requirements:
        - Proven technical expertise
        - Min 500K $INK staked
        - 2-year commitment
  ###### 3. Node Operator Council
      - 5 members
      - Elected by active node operators
      - Focus: Network operations & rewards
      - Requirements:
      - Active node operation
      - Min 3 months history
      - Performance score >95%
  ###### 4. Development Fund Committee
      - 5 members
      - 3 elected by token holders
      - 2 appointed by core team
      - Focus: Ecosystem fund allocation

##### 2. Proposal System
###### Proposal Types
    1. Network Parameters
    - Reward rates
    - Fee structures
    - Node requirements
    - Threshold: >50% approval
    2. Technical Upgrades
    - Smart contract updates
    - Protocol changes
    - Security implementations
    - Threshold: >66% approval
    3. Treasury Allocation
    - Development grants
    - Marketing initiatives
    - Infrastructure investments
    - Threshold: >60% approval
    4. Emergency Actions
    - Security patches
    - Emergency pauses
    - Threshold: >75% approval
    
###### Proposal Process
    1. Discussion Phase (7 days)
    - Forum discussion
    - Community feedback
    - Technical review
    2. Formal Proposal (3 days)
    - Detailed specification
    - Implementation plan
    - Economic impact analysis
    3. Voting Period (5 days)
    - On-chain voting
    - Real-time results
    - Vote locking
    4. Implementation (if passed)
    - Technical deployment
    - Community notification
    - Progress tracking
##### 3. Economic Rights
###### Fee Distribution
    - 70% to Node Operators
    - 20% to Treasury
    - 10% to Token Burn
###### Staking Benefits
    - Enhanced voting power
    - Fee share participation
    - Priority access to features
###### Developer Incentives
    - Grant access
    - Reduced platform fees
    - Governance participation

##### 4. Risk Management
###### Security Measures
    - Multi-sig requirements
    - Time-locks on major changes
    - Emergency pause mechanisms
###### Checks and Balances
    - Technical Committee veto power
    - Community override mechanism
    - Gradual parameter adjustment

##### 5. Future Governance Evolution
###### Phase 1: Foundation-Guided (Months 1-6)
    - Core team maintains significant influence
    - Focus on stability and growth
    - Community input gathering
###### Phase 2: Community Transition (Months 7-12)
    - Gradual power transfer
    - Committee formations
    - Initial proposal testing
###### Phase 3: Full Decentralization (Month 13+)
    - Complete community governance
    - Core team advisory role
    - Self-sustaining ecosystem

##### 6. Governance Analytics
###### KPIs to Track
    - Proposal participation rate
    - Vote distribution
    - Implementation success rate
    - Community engagement metrics

#### 5. Smart-Contract-Implementation
          
          // SPDX-License-Identifier: MIT
          pragma solidity ^0.8.17;
          
          import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
          import "@openzeppelin/contracts/access/AccessControl.sol";
          import "@openzeppelin/contracts/security/Pausable.sol";
          import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
          
          /**
           * @title KrakenToken
           * @dev Main token contract for KrakenNet ecosystem
           */
          contract KrakenToken is ERC20, AccessControl, Pausable, ReentrancyGuard {
              bytes32 public constant GOVERNANCE_ROLE = keccak256("GOVERNANCE_ROLE");
              bytes32 public constant OPERATOR_ROLE = keccak256("OPERATOR_ROLE");
              
              uint256 public constant TOTAL_SUPPLY = 1_000_000_000 * 10**18;  // 1B tokens
              
              // Vesting schedule structure
              struct VestingSchedule {
                  uint256 total;
                  uint256 released;
                  uint256 startTime;
                  uint256 cliff;
                  uint256 duration;
                  bool revocable;
              }
              
              // Mapping from address to vesting schedule
              mapping(address => VestingSchedule) public vestingSchedules;
              
              // Staking parameters
              struct Stake {
                  uint256 amount;
                  uint256 startTime;
                  uint256 multiplier;
                  bool isNodeOperator;
              }
              
              mapping(address => Stake) public stakes;
              
              // Events
              event TokensVested(address indexed beneficiary, uint256 amount);
              event TokensStaked(address indexed user, uint256 amount);
              event StakeWithdrawn(address indexed user, uint256 amount);
              event RewardsClaimed(address indexed user, uint256 amount);
              
              constructor() ERC20("KrakenNet", "INK") {
                  _setupRole(DEFAULT_ADMIN_ROLE, msg.sender);
                  _setupRole(GOVERNANCE_ROLE, msg.sender);
                  _mint(msg.sender, TOTAL_SUPPLY);
              }
              
              /**
               * @dev Creates a vesting schedule for an address
               */
              function createVestingSchedule(
                  address beneficiary,
                  uint256 amount,
                  uint256 cliffDuration,
                  uint256 vestingDuration,
                  bool revocable
              ) external onlyRole(GOVERNANCE_ROLE) {
                  require(vestingSchedules[beneficiary].total == 0, "Schedule exists");
                  require(amount > 0, "Amount = 0");
                  
                  vestingSchedules[beneficiary] = VestingSchedule({
                      total: amount,
                      released: 0,
                      startTime: block.timestamp,
                      cliff: cliffDuration,
                      duration: vestingDuration,
                      revocable: revocable
                  });
                  
                  _transfer(msg.sender, address(this), amount);
              }
              
              /**
               * @dev Releases vested tokens for msg.sender
               */
              function releaseVestedTokens() external nonReentrant {
                  VestingSchedule storage schedule = vestingSchedules[msg.sender];
                  require(schedule.total > 0, "No schedule");
                  
                  uint256 releasable = _calculateReleasable(schedule);
                  require(releasable > 0, "Nothing to release");
                  
                  schedule.released += releasable;
                  _transfer(address(this), msg.sender, releasable);
                  
                  emit TokensVested(msg.sender, releasable);
              }
              
              /**
               * @dev Calculates releasable tokens for a schedule
               */
              function _calculateReleasable(VestingSchedule memory schedule) 
                  private 
                  view 
                  returns (uint256) 
              {
                  if (block.timestamp < schedule.startTime + schedule.cliff) {
                      return 0;
                  }
                  
                  if (block.timestamp >= schedule.startTime + schedule.duration) {
                      return schedule.total - schedule.released;
                  }
                  
                  uint256 timeFromStart = block.timestamp - schedule.startTime;
                  uint256 vestedAmount = (schedule.total * timeFromStart) / schedule.duration;
                  
                  return vestedAmount - schedule.released;
              }
              
              /**
               * @dev Stake tokens for governance and rewards
               */
              function stake(uint256 amount, bool asNodeOperator) external nonReentrant {
                  require(amount > 0, "Amount = 0");
                  require(balanceOf(msg.sender) >= amount, "Insufficient balance");
                  
                  stakes[msg.sender] = Stake({
                      amount: amount,
                      startTime: block.timestamp,
                      multiplier: asNodeOperator ? 200 : 150,  // 2x for nodes, 1.5x for regular
                      isNodeOperator: asNodeOperator
                  });
                  
                  _transfer(msg.sender, address(this), amount);
                  emit TokensStaked(msg.sender, amount);
              }
              
              // Additional functions for governance, rewards, and network operations...
          }
#### 6. KPI Tracking System [KPI Link](https://claude.site/artifacts/aa5dd1da-9620-4ae9-ad75-6b2fcd041318)

### RESTFul API Tutorial (Not Yet Public Only Example)
#### Objective
  - Describes the usage procedure of the RESTFul API of the Kraken platform that generates media content corresponding to user input using generative AI technology and the detailed API functions.

#### Target
  - Companies / users who want to receive AI-based media content corresponding to input data end-to-end
  - Developers or researchers of services (Web/Mobile/PC) based on HTTP/HTTPS web protocol


#### Step-1: Get Token (POST)
1. Description
  - The first step to use the Kraken RESTFul API is to issue an authentication key for the entered account.
  - The issued authentication key must be passed along with the Authorization key in all API headers to enable normal API use.
  - The expiration time (Expiry) of the issued authentication key is 24 hours.
1. Example (Authorization)
    - Request
          curl -X 'POST' \
            'https://api.gokraken.net/kraken/auth/sign-in' \
            -H 'accept: application/json' \
            -H 'Content-Type: application/json' \
            -d '{
            "id": "ID",
            "password": "Password"
          }'
      
    - Response
        {
          "accessToken":"Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9"
        }

#### Step-2: Create AI Model (POST)
  1.  Description
    - Send a request to create AI results based on input data (Video, Image) using the Creation API provided by the Kraken platform.
    - If the creation request is registered normally, the item's unique number (`jobId`) is returned.
    - You can check the item download and creation status using `jobId`.
  1. Example (Video →NeRF2Mesh→ 3DMesh)
      - Request
        curl -X 'POST' \
          'https://api.gokraken.net/kraken/model/nerf2mesh/create' \
          -H 'accept: application/json' \
          -H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9' \
          -H 'Content-Type: multipart/form-data' \
          -F 'itemName=NeRFTest' \
          -F 'epoch=50' \
          -F 'payload=@new_dragon.mp4;type=video/mp4'
        
      - Response
          {
              "jobId": "257a59cc-0a3e-4ce7-b7e3-17f3ce6a8041",
              "queueSize": "0",
              "trainingTimePerJob": "10m"
          }
          
#### Step-3 : GET Item Info (GET)
   1. Description
      - Check the item creation status using the item unique number (`jobId`).

   1. Example (Video → NeRF2Mesh → 3DMesh)
        - Request
          curl -X 'GET' \
           'https://api.gokraken.net/kraken/item/my-item/257a59cc-0a3e-4ce7-b7e3-17f3ce6a8041' \
           -H 'accept: application/json' \
           -H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9'
          
        - Response
            {
             "jobId": "257a59cc-0a3e-4ce7-b7e3-17f3ce6a8041",
             "itemName": "NeRFTest",
             "model": "nerf2mesh",
             "jobStatus": "success",
             "createdAt": "2024-08-27T14:42:10.845859Z",
             "requestedAt": "2024-08-27T14:38:11.811489Z"
            }


#### Step-4 : Download Item (GET)
  1. Description
      - A creation request is registered and the returned item unique number (`jobId`) is used to download the item.

  1. Example (Video → NeRF2Mesh → 3DMesh)
      - Request
          curl -X 'GET' \
           'https://api.gokraken.net/kraken/item/download/257a59cc-0a3e-4ce7-b7e3-17f3ce6a8041?type=model' \
           -H 'accept: application/octet-stream' \
           -H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9' \
           --output nerf2mesh.glb
        
    - Response
          HTTP/2 200
          date: Tue, 27 Aug 2024 07:23:55 GMT
          content-type: model/gltf-binary
          content-length: 747160
          content-disposition: attachment; filename=mesh_1.glb
          cache-control: no-cache

# Krakolabs Service Interface API List

# 1\. Introduction

- - -

## 1.1 Purpose

* Passes the Atlas Backend API framework feature items.    

# 2\. Authorization API

- - -

## 2.1 POST atlas/auth/sign-up

* This is an email-based membership registration API.    

### 2.1.1 Request

**Info**

|     |     |     |
| --- | --- | --- |
| **Info** |     |     |
| Method | `POST` |     |
| Content-Type | `application/json` |     |
| **Body** |     |     |
| **Key** | **Type** | **Description** |
| `email` | `string` | User Email |
| `password` | `string` | User Password<br><br>* At least 10 characters<br> <br>* Contains at least 1 uppercase and lowercase letter<br> <br>* Contains at least 1 special character<br> <br>* Contains at least 1 number |
| `nickname` | `string` | User nickname |

### 2.1.2 Response

#### 2.1.2.1 Success

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 201 | Membership registration completed | |
| **Body** |     |     |
| **key** | **Type** | **Description** |
|     |     |     |

#### 2.1.2.2 Fail

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 400 | * If the input is invalid | |
| 409 | * If the email is duplicated | |
| 500 | * Email external module error or unexpected error | |
| **Body** |     |     |
| **key** | **Type** | **Description** |
| `message` | `string` | Response information |
- - -

## 2.2 POST atlas/auth/sign-In

* This is an email-based login API.    

### 2.2.1 Request

**Info**

|     |     |     |
| --- | --- | --- |
| **Info** |     |     |
| Method | `POST` |     |
| Content-Type | `application/json` |     |
| **Body** |     |     |
| **Key** | **Type** | **Description** |
| `id` | `string` | User Email |
| `password` | `string` | User Password (10 characters or more) |

### 2.2.2 Response

#### 2.2.2.1 Success

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 201 | Login Complete | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `accessToken` | `string` | accessToken information including account, session information (Expire: 1h)<br><br>Ex)<br><br>`{ accessToken: "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIwMUo2NlYwS0hXQzMyMThTOUEyVzE2RlQ0MSIsInJvbGUiOiJTVVBFUl9BRE1JTiIsImlhdCI6MTcyNTg0MzYxNiwiZXhwIjoxNzI1OTMwMDE2fQ.ggTmD5JnjvZa-CwOQqIt_fnclrzHORAxVMfwdcWD0v8" }` |
| `refreshToken` | `string` | refreshToken information including session information (Expire: 14d)<br><br>Ex)<br><br>`{ "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIwMUo4NkVNTUpGUVhUU1dYWFFOUjI1TUhLVyIsInNlc3Npb25JZCI6Ijg4Mzk1NjdjLTljOTktNGQxMy1hZWNlLTIwMDZmYTg3OTJlNiIsImlhdCI6MTcyNjc5MzUzNiwiZXhwIjoxNzI4MDAzMTM2fQ.XNUDCHAHd7z1v4HiGW9E1rLwFqr6Hntxe2Y33FNyyc8" }` |

Response Body Example

`{ "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIwMUo4NkVNTUpGUVhUU1dYWFFOUjI1TUhLVyIsInJvbGUiOiJTVVBFUl9BRE1JTiIsInNlc3Npb25JZCI6Ijg4Mzk1NjdjLTljOTktNGQxMy1hZWNlLTIwMDZmYTg3OTJlNiIsImlhdCI6MTcyNjc5MzUzNiwiZXhwIjoxNzI2Nzk3MTM2fQ.mqR9R3xHoEpwcsJGYTBkzr_98XggeIAHjnU1JSuVEtg", "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIwMUo4NkVNTUpGUVhUU1dYWFFOUjI1TUhLVyIsInNlc3Npb25JZCI6Ijg4Mzk1NjdjLTljOTktNGQxMy1hZWNlLTIwMDZmYTg3OTJlNiIsImlhdCI6MTcyNjc5MzUzNiwiZXhwIjoxNzI4MDAzMTM2fQ.XNUDCHAHd7z1v4HiGW9E1rLwFqr6Hntxe2Y33FNyyc8" }`

#### 2.2.2.2 Fail

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 401 | * If the email address is incorrect<br> <br>* If the password is incorrect | |
| 403 | * Email authentication has not been completed. | |
| 409 | * If the user signed up with Google Social Login | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `message` | `string` | Response information |
- - -

## 2.3 POST atlas/auth/sign-out

* This is a logout API.

* Expires the session and expires the tokens.

### 2.3.1 Request

**Info**

|     |     |
| --- | --- |
| **Info** |     |
| Method | `POST` |
| Content-Type | `application/json` |
| **Header** |     |
| `Authorization` | Token information including account information<br><br>* “Bearer `accessToken`” |

### 2.3.2 Response

#### 2.3.2.1 Success

**Code**

|     |     |
| --- | --- |
| **Code** |     |
| **Code** | **Description** |
| 200 | Logout complete |

#### 2.3.2.2 Fail

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 401 | * In case of invalid token | |
| 500 | * Unexpected system error | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `message` | `string` | Response information |

- - -

## 2.4 POST atlas/auth/confirmation-emails/resend

* This is an API for resending authentication emails.    

### 2.4.1 Request

**Info**

|     |     |     |
| --- | --- | --- |
| **Info** |     |     |
| Method | `POST` |     |
| Content-Type | `application/json` |     |
| **Body** |     |     |
| **Key** | **Type** | **Description** |
| `email` | `string` | User Email |

### 2.4.2 Response

#### 2.4.2.1 Success

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 201 | Verification email resend complete | |
| **Body** |     |     |
| **key** | **Type** | **Description** |
|     |     |     |

#### 2.4.2.2 Fail

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 404 | * If there is no member corresponding to the email | |
| 500 | * If an email external module error or an unexpected error occurs | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `message` | `string` | Response information |

- - -

## 2.5 GET atlas/auth/confirm-email

* This is an email authentication API.    

### 2.5.1 Request

**Info**

|     |     |     |
| --- | --- | --- |
| **Info** |     |     |
| Method | `GET` |     |
| Content-Type | `application/json` |     |
| **Query** |     |     |
| **Key** | **Type** | **Description** |
| `email` | `string` | User Email |
| `userId` | `string` | Unique user identifier value (uuid) |

### 2.5.2 Response

#### 2.5.2.1 Success

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 200 | 이메일 인증 완료 |     |
| **Body** |     |     |
| **key** | **Type** | **Description** |
|     |     |     |

#### 2.5.2.2 Fail

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 400 | * If email authentication fails | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `message` | `string` | Response information |
- - -

## 2.6 GET atlas/auth/confirm-invite-email

* This is a group invitation authentication API.    

### 2.6.1 Request

**Info**

|     |     |     |
| --- | --- | --- |
| **Info** |     |     |
| Method | `GET` |     |
| Content-Type | `application/json` |     |
| **Query** |     |     |
| **Key** | **Type** | **Description** |
| `email` | `string` | 사용자 Email |
| `userId` | `string` | Unique user identifier value (uuid) |
| `groupId` | `string` | Unique group identifier value (uuid) |

### 2.6.2 Response

#### 2.6.2.1 Success

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 200 | Membership withdrawal completed | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `email` | `string` | `email` value of the member who withdrew |

#### 2.6.2.2 Fail

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 401 | * If the token value is invalid | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `message` | `string` | Response information |

- - -

## 2.7 POST atlas/auth/reset-password

* This is a password reset API.    

### 2.7.1 Request

**Info**

|     |     |     |
| --- | --- | --- |
| **Info** |     |     |
| Method | `POST` |     |
| Content-Type | `application/json` |     |
| **Body** |     |     |
| **Key** | **Type** | **Description** |
| `email` | `string` | User Email |
### 2.7.2 Response

#### 2.7.2.1 Success

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 201 | Temporary password email sent | |
| **Body** |     |     |
| **key** | **Type** | **Description** |
|     |     |     |

#### 2.7.2.2 Fail

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 404 | * If the user with email fails to be retrieved | |
| 500 | * If an error occurs in the email sending module | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `message` | `string` | Response information |

- - -

## 2.8 PATCH atlas/auth/password

* This is a password change API.    

### 2.8.1 Request

**Info**

|     |     |     |
| --- | --- | --- |
| **Info** |     |     |
| Method | `POST` |     |
| Content-Type | `application/json` |     |
| **Header** |     |     |
| `Authorization` | Token information including account information<br><br>* “Bearer `accessToken`” | |
| **Key** | **Type** | **Description** |
| `password` | `string` | Password to change |

### 2.8.2 Response

#### 2.8.2.1 Success

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 200 | Password change completed | |
| **Body** |     |     |
| **key** | **Type** | **Description** |
|     |     |     |

#### 2.8.2.2 Fail

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 400 | * If the input value is invalid | |
| 404 | * If the user with userId fails to be retrieved | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `message` | `string` | Response information |

- - -

## 2.9 DELETE atlas/auth/user

* This is a membership withdrawal API.    

### 2.9.1 Request

**Info**

|     |     |     |
| --- | --- | --- |
| **Info** |     |     |
| Method | `DELETE` |     |
| Content-Type | `application/json` |     |
| **Header** |     |     |
| `Authorization` | Token information including account information<br><br>* “Bearer `accessToken`” | |
| **Key** | **Type** | **Description** |
|     |     |     |

### 2.9.2 Response

#### 2.9.2.1 Success

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 200 | Membership withdrawal completed | |
| **Body** |     |     |
| **key** | **Type** | **Description** |
|     |     |     |

#### 2.9.2.2 Fail

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 401 | * If the token value is invalid | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `message` | `string` | Response information |
- - -

## 2.10 POST atlas/auth/google

* This is Google Social Login API.    

### 2.10.1 Request

**Info**

|     |     |     |
| --- | --- | --- |
| **Info** |     |     |
| Method | `POST` |     |
| Content-Type | `application/json` |     |
| **Key** | **Type** | **Description** |
| `idToken` | `string` | Google Social Login idToken value |
### 2.10.2 Response

#### 2.10.2.1 Success

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 201 | Google Social Login Complete | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `accessToken` | `string` | accessToken information including account and session information (Expire: 1h)<br><br>Ex) |
| `refreshToken` | `string` | refreshToken information including session information (Expire: 14d)<br><br>Ex) |

Response Body Example

#### 2.10.2.2 Fail

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 401 | * If Google authentication information is invalid | |
| 409 | * If you have already signed up with the same email | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `message` | `string` | Response information |

- - -

## 2.11 POST atlas/refresh/accessToken

* This is an accessToken reissue API.    

### 2.11.1 Request

**Info**

|     |     |
| --- | --- |
| **Info** |     |
| Method | `POST` |
| Content-Type | `application/json` |
| **Header** |     |
| `Authorization` | Token information including account information<br><br>* “Bearer `refreshToken`” |
### 2.11.2 Response

#### 2.11.2.1 Success

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 200 | accessToken reissue success | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `accessToken` | `string` | accessToken information including account and session information (Expire: 1h)<br><br>Ex) |
#### 2.11.2.2 Fail

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 401 | * If an invalid refreshToken is used | |
| 500 | * Unexpected system error | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `message` | `string` | Response information |

- - -

## 2.12 GET atlas/me

* This is my information inquiry API.    

### 2.12.1 Request

**Info**

|     |     |     |
| --- | --- | --- |
| **Info** |     |     |
| Method | `GET` |     |
| Content-Type | `application/json` |     |
| **Header** |     |     |
| `Authorization` | Token information including account information<br><br>* “Bearer `accessToken`” | |
| **Key** | **Type** | **Description** |
| `idToken` | `string` | Google Social Login idToken value |

### 2.12.2 Response

#### 2.12.2.1 Success

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 200 | My information search success | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `nickname` | `string` | Current user's `nickname` value |
| `email` | `string` | Current user's `email` value |

#### 2.12.2.2 Fail

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 404 | * If there is no member | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `message` | `string` | Response information |

- - -

# 3\. AI Model Creation API

- - -

## 3.1 POST /atlas/item/decal

* Send a request to create a 3D model (\*.glb) using the Decal AI model.    
    

### 3.1.1 Request

**Info**

|     |     |     |
| --- | --- | --- |
| **Info** |     |     |
| Method | `POST` |     |
| Content-Type | `multipart/form-data` |     |
| **Header** |     |     |
| `Authorization` | Token information including account information<br><br>* “Bearer `accessToken`” | |
| **Body** | | |
| **Key** | **Type** | **Description** |
| `itemName` | `string` | Item name |
| `deviceType` | `string` | Recording device type (currently only android) |
| `fileName` | `string` | Input video file name (considering whether to remove it by defining inputFormat name rules) |
| `payLoad` | `file` | Input video file (mp4, 20~60 seconds) |

### 3.1.2 Response

#### 3.1.2.1 Success

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 201 | 3D Model Creation Request Completed | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `requestId` | `string` | Unique ID of the item to be created |

#### 3.1.2.2 Fail

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 400 | * Invalid request syntax<br> <br>* Error while creating compressed file<br> <br>* Duplicate uuid error for requestId<br> <br>* Error when the corresponding item already exists | |
| 401 | * Invalid Token information | |
| 500 | * External module error (DB) | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `message` | `string` | Response information |

- - -

## 3.2 POST /atlas/item/nerf2mesh

* Send a request to create a 3D model (\*.glb) using the Nerf2mesh AI model.  

### 3.2.1 Request

**Info**

|     |     |     |
| --- | --- | --- |
| **Info** |     |     |
| Method | `POST` |     |
| Content-Type | `multipart/form-data` |     |
| **Header** |     |     |
| `Authorization` | Token information including account information<br><br>* “Bearer `accessToken`” | |
| **Body** | | |
| **Key** | **Type** | **Description** |
| `resize` | `boolean` | Change the resolution of the input video (if 1, change the resolution of the input video to HD) |
| `itemName` | `string` | Item name |
| `Epoch` | `number` | Number of learning times (Stage 0 & 1 Epoch, 1 <= Epoch <= 100) |
| `payLoad` | `file` | Input video file (mp4)<br><br>* Length: 20 seconds ~ 180 seconds<br> <br>* Resolution: HD ~ FHD<br> <br>* Capacity: 50MB ~ 300MB |

### 3.2.2 Response

#### 3.2.2.1 Success

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 201 | 3D Model Creation Request Completed | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `requestId` | `string` | Unique ID of the item to be created |

#### 3.2.2.2 Fail

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 400 | * Invalid request syntax<br> <br>* Error while creating compressed file<br> <br>* Duplicate uuid error for requestId<br> <br>* Error when the corresponding item already exists | |
| 401 | * Invalid Token information | |
| 500 | * External module error (DB) | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `message` | `string` | Response information |

- - -

## 3.3 POST /atlas/item/musepose

* Send a request to create a 3D model (\*.glb) using the musepose AI model.
### 3.3.1 Request

**Info**

|     |     |     |
| --- | --- | --- |
| **Info** |     |     |
| Method | `POST` |     |
| Content-Type | `multipart/form-data` |     |
| **Header** |     |     |
| `Authorization` | Token information including account information<br><br>* “Bearer `accessToken`” | |
| **Body** | | |
| **Key** | **Type** | **Description** |
| `itemName` | `string` | Item name |
| `Epoch` | `number` | Number of learning (Stage 0 & 1 Epoch, 1 <= Epoch <= 100) |
| `Image` | `file` | Input image file<br><br>* File extension: png \| jpg \| jpeg<br> <br>* Resolution: Vertical image<br> <br>* Size: Less than 10MB |
| `payLoad` | `file` | Input video file (mp4)<br><br>* Length: Less than 20 seconds<br> <br>* Resolution: Vertical video<br> <br>* Size: Less than 20MB |

### 3.3.2 Response

#### 3.3.2.1 Success

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 201 | 3D Model Creation Request Completed | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `requestId` | `string` | Unique ID of the item to be created |

#### 3.3.2.2 Fail

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 400 | * Invalid request syntax<br> <br>* Error while creating compressed file<br> <br>* Duplicate uuid error for requestId<br> <br>* Error when the corresponding item already exists | |
| 401 | * Invalid Token information | |
| 500 | * External module error (DB) | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `message` | `string` | Response information |

- - -

## 3.4 POST /atlas/item/instant-mesh

* Send a request to create a 3D model (\*.glb) using the instant-mesh AI model.    

### 3.4.1 Request

**Info**

|     |     |     |
| --- | --- | --- |
| **Info** |     |     |
| Method | `POST` |     |
| Content-Type | `multipart/form-data` |     |
| **Header** |     |     |
| `Authorization` | Token information including account information<br><br>* “Bearer `accessToken`” | |
| **Body** | | |
| **Key** | **Type** | **Description** |
| `itemName` | `string` | Item name |
| `Epoch` | `number` | Number of learning (Stage 0 & 1 Epoch, 1 <= Epoch <= 100) |
| `Image` | `file` | Input image file<br><br>* File extension: png \| jpg \| jpeg<br> <br>* Resolution: 300 \* 300 or more<br> <br>* Capacity: 10mb or less |

### 3.4.2 Response

#### 3.4.2.1 Success

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 201 | 3D Model Creation Request Completed | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `requestId` | `string` | Unique ID of the item to be created |

#### 3.4.2.2 Fail

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 400 | * Invalid request syntax<br> <br>* Error while creating compressed file<br> <br>* Duplicate uuid error for requestId<br> <br>* Error when the corresponding item already exists | |
| 401 | * Invalid Token information | |
| 500 | * External module error (DB) | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `message` | `string` | Response information |

- - -

## 3.5 POST /atlas/item/gs3d

* Send a request to create a 3D model (\*.ply) using the Gaussian-Splatting AI model.
  
### 3.5.1 Request

**Info**

|     |     |     |
| --- | --- | --- |
| **Info** |     |     |
| Method | `POST` |     |
| Content-Type | `multipart/form-data` |     |
| **Header** |     |     |
| `Authorization` | Token information including account information<br><br>* “Bearer `accessToken`” | |
| **Body** | | |
| **Key** | **Type** | **Description** |
| `itemName` | `string` | Item name |
| `Epoch` | `number` | Number of learning (Stage 0 & 1 Epoch, 1 <= Epoch <= 100) |
| `Payload` | `file` | Input video file<br><br>* File extension: mp4<br> <br>* Resolution: FHD or lower<br> <br>* Capacity: 300mb or lower<br> <br>* Length: 180 seconds or less |

### 3.5.2 Response

#### 3.5.2.1 Success

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 201 | 3D Model Creation Request Completed | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `requestId` | `string` | Unique ID of the item to be created |

#### 3.5.2.2 Fail

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 400 | * Invalid request syntax<br> <br>* Error while creating compressed file<br> <br>* Duplicate uuid error for requestId<br> <br>* Error when the corresponding item already exists | |
| 401 | * Invalid Token information | |
| 500 | * External module error (DB) | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `message` | `string` | Response information |
- - -

# 4\. Item API

- - -

## 4.1 GET atlas/item/detail/{requestId}

* This is an API that searches for an item corresponding to `requestId`.    

### 4.1.1 Request

**Info**

|     |     |
| --- | --- |
| **Info** |     |
| Method | `GET` |
| **Header** |     |
| `Authorization` | Token information including account information<br><br>* “Bearer `accessToken`” |
| **URL Parameter** | |
| `requestId` | Unique item ID |

### 4.1.2 Response

#### 4.1.2.1 Success

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 200 | Completed processing of specific item lookup request | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `requestId` | `string` | Unique item number |
| `Status` | `string` | Item creation request status<br><br>`PROGRESS`: Item creation request is still in the queue or learning is in progress<br><br>`OK`: Item creation is complete<br><br>`NG`: Learning for item creation failed |
| `modelId` | `string` | Unique identifier of the model used to create the item<br><br>* `nerf2mesh : 1`<br> <br>* `gaussian-splatting : 2`<br> <br>* `decal : 3`<br> <br>* `MusePose : 4`<br> <br>* `InstantMesh : 5` |
| `modelName` | `string` | Model name used to create the item<br><br>* `nerf2mesh`<br> <br>* `gaussian-splatting`<br> <br>* `decal`<br> <br>* `MusePose`<br> <br>* `InstantMesh` |
| `uploaderId` | `string` | Unique identifier of the user who requested the creation |
| `uploaderNickname` | `string` | Nickname of the user who requested the creation |
Response Body Example

#### 4.1.2.2 Fail

**Code**

**Code**

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 401 | Invalid Token Information | |
| 500 | API Server Internal Error | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `message` | `string` | Response Information |

- - -

## 4.2 GET atlas/item/download/{requestId}

* Download generated items corresponding to `requestId`    

### 4.2.1 Request

**Info**

|     |     |
| --- | --- |
| **Info** |     |
| Method | `GET` |
| **URL** |     |
| `requestId` | Unique item ID |
| **Query** | |
| `code` | Specifies the model result format of the generated item (optional).<br><br>See the Code table below. |
| **Header** | |
| `Authorization` | Token information including account information<br><br>* “Bearer `accessToken`” |
Code Table

### 1\. `gaussian-splatting`

| `code` Value | target file |
| --- | --- |

| `code` Value | target file |
| --- | --- |
| 1(default) | \*`.ply` |

### 2\. `InstantMesh`

| `code` Value | target file |
| --- | --- |

| `code` Value | target file |
| --- | --- |
| 1(default) | \*`.glb` (Default) |
| 2   | \*`.obj` |
| 2   | \*`.obj` |

### 3\. `decal`

|     |     |
| --- | --- |
| `code` Value | target file |
|     |     |
| --- | --- |
| `code` Value | target file |
| 1(default) | \*`.glb` (Default) |
| 2   | \*`.gif` |
| 3   | \*`.tar.gz` |

### 4\. `nerf2mesh`

| `code` Value | target file |
| --- | --- |

| `code` Value | target file |
| --- | --- |
| 1(default) | \*`_1.glb` (Default) |
| 2   | \*`_0.glb` |
| 3   | \*`.gif` |

### 4.2.2 Response

#### 4.2.2.1 Success

**Code**

|     |     |
| --- | --- |
| **Code** |     |
| **Code** | **Description** |
| 200 | Item download request processing completed |
| **Body** |     |
| **${itemName}.${ext}** |     |

#### 4.2.2.2 Fail

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 401 | Invalid Token Information | |
| 404 | If the history for the item cannot be found | |
| 500 | API Server Internal Error | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `message` | `string` | Response Information |

- - -

## 4.3 GET atlas/item/my/{ModelId}

* API to retrieve the list of items created by the current user

* Retrieves by model

### 4.3.1 Request

**Info**

|     |     |     |
| --- | --- | --- |
| **Info** |     |     |
| Method | `GET` |     |
| **URL** |     |     |
| `ModelId` | Required | AI model unique ID |
| `orderBy` | Required | Sorting criteria field (currently only createdAt is supported) |
| `desc` | Required | Sorting direction (true: ascending, false: descending) |
| `cursor` | Optional | Cursor value (currently only createdAt is supported)<br><br>* If null, the most recent data will be retrieved |
| `limit` | Optional | Number of items per page<br><br>* Default value 10<br> <br>* Maximum value 50 |

### 4.3.2 Response

#### 4.3.2.1 Success

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 200 | User-created item list query request processing completed | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `requestId` | `string` | Item unique number |
| `status` | `string` | Item creation request status<br><br>`PROGRESS`: Item creation request is still in the queue or learning in progress<br><br>`OK`: Item creation is complete<br><br>`NG`: Learning for item creation failed |
| `model` | `string` | Model name used to create the item<br><br>* `nerf2mesh`<br> <br>* `gaussian-splatting`<br> <br>* `decal`<br> <br>* `MusePose`<br> <br>* `InstantMesh` |
| `itemName` | `string` | Item name |
| `createdAt` | `string` | Item creation date |
| `hasNextItem` | `boolean` | Whether there are more items to retrieve |
| `nextCursor` | `string` \| `null` | Cursor data for retrieving the next page (null if there is no next page) |
Response Body Example

#### 4.3.2.2 Fail

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 401 | Invalid Token Information | |
| 500 | API Server Internal Error | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `message` | `string` | Response Information |
- - -

## 4.4 GET atlas/item/detail/my/{requestId}

* This is an API that searches for information on the `requestId` item created by the user.    

### 4.4.1 Request

**Info**

|     |     |
| --- | --- |
| **Info** |     |
| Method | `GET` |
| **URL** |     |
| `requestId` | Unique item ID |

### 4.4.2 Response

#### 4.4.2.1 Success

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 200 | Item details inquiry request processing completed | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `requestId` | `string` | Item unique number |
| `itemName` | `string` | Item name |
| `status` | `string` | Item creation request status<br><br>`PROGRESS`: Item creation request is still in the queue or learning is in progress<br><br>`OK`: Item creation is complete<br><br>`NG`: Learning for item creation failed |
| `modelId` | `string` | Unique identifier of the model used to create the item<br><br>* `nerf2mesh : 1`<br> <br>* `gaussian-splatting : 2`<br> <br>* `decal : 3`<br> <br>* `MusePose : 4`<br> <br>* `InstantMesh : 5` |
| `modelName` | `string` | AI model name |
| `uploaderId` | `string` | Unique number of the uploading user |
| `uploaderNickname` | `string` | Nickname of the uploading user |
| `createdAt` | `string` | Item creation time |

#### 4.4.2.2 Fail

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 401 | Invalid Token Information | |
| 404 | If the item cannot be found | |
| 500 | API Server Internal Error | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `message` | `string` | Response Information |

- - -

## 4.5 DELETE atlas/item/{requestId}

* Delete the `requestId` item.    

### 4.5.1 Request

**Info**

|     |     |
| --- | --- |
| **Info** |     |
| Method | `DELETE` |
| **URL** |     |
| `requestId` | Unique item ID |
### 4.5.2 Response

#### 4.5.2.1 Success

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 200 | Item deletion request completed | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `requestId` | `string` | Item unique number |

#### 4.5.2.2 Fail

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 401 | Invalid Token Information | |
| 403 | If you try to delete an item that you do not own | |
| 404 | If the item cannot be found | |
| 500 | API Server Internal Error | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `message` | `string` | Response Information |
- - -

## 4.6 GET atlas/item/${type}/{requestId}

* Download metadata of `requestId` item.

* Metadata here refers to job\_log, output.zip, reply.tar.gz, request.tar.gz, thumbnail, etc.)

* Developed for viewing generated results during AI model development

### 4.6.1 Request

**Info**

|     |     |
| --- | --- |
| **Info** |     |
| Method | `GET` |
| **URL** |     |
| `requestId` | Unique item ID |
| `type` | Unique metadata type<br><br>* thumbnail<br> <br>* request<br> <br>* reply<br> <br>* output<br> <br>* log |

### 4.6.2 Response

#### 4.6.2.1 Success

**Code**

|     |     |
| --- | --- |
| **Code** |     |
| **Code** | **Description** |
| 200 | Item metadata download request completed |
| **Body** |     |
| **request.tar.gz** |     |

#### 4.6.2.2 Fail

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 401 | Invalid Token Information | |
| 404 | If the item cannot be found | |
| 500 | API Server Internal Error | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `message` | `string` | Response Information |

- - -

# 5\. Role API

- - -

## 5.1 GET atlas/role

* All Role query APIs.

* Roles have an inheritance relationship, and the parent role includes all permissions of the child role.
        

### 5.1.1 Request

**Info**

|     |     |
| --- | --- |
| **Info** |     |
| Method | `GET` |
| Content-Type | `application/json` |

### 5.1.2 Response

#### 5.1.2.1 Success

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 201 | All Roles Completed | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `id` | `string` | Unique identifier of the role. (uuid) |
| `name` | `string` | Role name. (ex. Seoul National University) |
| `description` | `string` | Role description. |
| `createdAt` | `string` | Role creation date. |
| `updatedAt` | `string` | Role modification date. |

#### 5.1.2.2 Fail

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 500 | * Unexpected system error | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `message` | `string` | Response information |

- - -

# 6\. Basic API

- - -

## 6.1 GET atlas/version

* This is the version query API.

### 6.1.1 Request

**Info**

|     |     |
| --- | --- |
| **Info** |     |
| Method | `GET` |
| Content-Type | `application/json` |

### 6.1.2 Response

#### 6.1.2.1 Success

|     |     |
| --- | --- |
|     |     |

|     |     |
| --- | --- |
|     |     |
| **Description** |     |
| All Roles Query Complete | |
| | | |
| **Type** | **Description** |
| `string` | Version Information |

#### 6.1.2.2 Fail

**Code**

|     |     |     |
| --- | --- | --- |
| **Code** |     |     |
| **Code** | **Description** |     |
| 500 | * Unexpected system error | |
| **Body** | | |
| **key** | **Type** | **Description** |
| `message` | `string` | Response information |

- End -


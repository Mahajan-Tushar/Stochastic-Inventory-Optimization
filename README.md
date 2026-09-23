# Stochastic Inventory Optimization under Demand Uncertainty using Gurobi and OR-Tools

This project presents an **end-to-end data-driven framework for multi-SKU inventory replenishment under uncertain demand** using historical sales from the **Walmart M5 dataset**. The framework constructs **forecast-based demand scenarios** and solves a multi-period **mixed-integer linear program (MILP)** to determine replenishment quantities while balancing operating cost, product availability, and inventory capacity.

The pipeline integrates **historical sales preprocessing**, **demand forecasting and scenario generation**, a **forecast-driven base-stock baseline**, and two independently implemented optimization backends - **Gurobi** and **Google OR-Tools**. Each solver's optimized policy is evaluated **separately against the same baseline** on a chronological holdout period.

### Technical Pipeline

1. **Historical Sales Preparation**  
   Load the Walmart M5 sales data, select 6 active SKU–store series, and aggregate daily sales into weekly observations. Use **64 weeks for training** and reserve the following **6 weeks as an untouched chronological holdout**.
2. **Demand Forecasting and Scenario Generation**  
   Estimate future weekly demand from training history and create **16 uncertainty scenarios** using training forecast residuals. Demand scenarios capture variation around expected sales without using holdout observations to determine replenishment decisions.
3. **Baseline Inventory Planning**  
   Generate a **fixed, forecast-driven base-stock-style order schedule** using predicted demand, projected inventory, and a safety buffer. Select the buffer using training scenarios and the modeled service/cost trade-off; fix the entire schedule before holdout evaluation.
4. **Stochastic Inventory Optimization**  
   Formulate a **multi-SKU, multi-period MILP** that chooses a common replenishment schedule across demand scenarios. The model minimizes purchase, fixed ordering, expected holding, stockout, and disposal costs subject to order limits, inventory balance, warehouse capacity, and an **aggregate expected fill-rate target**.
5. **Independent Solver Implementation and Evaluation**  
   Evaluate each optimized order schedule against the *same baseline* with a common forward simulator on the held-out sales observations. Report modeled cost, its components, fill rate, lost sales, ordered units, disposal, and inventory trajectories for each policy.

### Inventory Optimization Configuration

The demonstration uses **6 SKU–store series**, a **6-week replenishment horizon**, and **16 training demand scenarios**, with a **95% aggregate expected fill-rate target** in the optimization model. Replenishment decisions are made before the demand scenario is realized; inventory and lost-sales outcomes vary by scenario. The model includes **per-SKU ordering limits** and a **warehouse-capacity constraint**.

The financial and operational parameters—including purchasing and holding costs, stockout penalties, initial inventory, warehouse capacity, disposal, and immediate order receipt—are **modeling assumptions**, not observed Walmart operating costs or inventory records.

### Holdout Impact

The optimized replenishment policy reduced total modeled cost by **11.89% compared with the forecast-driven baseline** on the chronological holdout, demonstrating the cost-saving potential of explicitly accounting for demand uncertainty in inventory planning.

### Evaluation and Outputs

Each solver presents its **own baseline-versus-optimized policy comparison** on the same chronological holdout, including:

- **Modeled total cost** and purchase, ordering, holding, stockout, and disposal cost components.
- **Observed-sales fill rate**, lost units, ordered units, disposed units, and average ending inventory.
- **Weekly order schedules**, inventory trajectories, and cost-component visualizations.

### Key Idea

Conventional inventory planning often relies on a point forecast and a fixed safety-stock rule. This project incorporates **demand uncertainty directly into replenishment optimization**, allowing the ordering schedule to account for alternative demand outcomes while respecting operational constraints. A common baseline and holdout simulator make the effect of each optimized policy interpretable without conflating policy improvement with solver-to-solver performance.

**Historical M5 Sales → Weekly Data Preparation → Demand Forecasting → Uncertainty Scenarios → Baseline & Stochastic MILP → Gurobi / OR-Tools (separately) → Holdout Policy Evaluation**

### Tech Stack

`Python` · `Pandas` · `NumPy` · `Matplotlib` · `Jupyter Notebook` · `Gurobi` · `Google OR-Tools` · `Walmart M5 Dataset` · `Mixed-Integer Linear Programming`

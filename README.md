# EPL_Predictions

## Project Overview

EPL_Predictions is a project aimed at predicting the outcomes of English Premier League (EPL) matches. The project includes data collection, preprocessing, model training, prediction, and visualization functionalities. The predictions are based on historical data and various machine learning models.

## Installation

To install the required dependencies, run the following command:

```bash
pip install -r requirements.txt
```

## Usage

### Running the Main Scripts

1. **app.py**: This script initializes a Gradio app interface for running the model.
   ```bash
   python app.py
   ```

2. **historicaldata_collection.py**: This script collects historical data and preprocesses it.
   ```bash
   python historicaldata_collection.py --config eplprediction/config/config.yaml --download
   ```

3. **main_script.py**: This script performs data scraping and processing to predict outcomes in the specified English Premier League configuration.
   ```bash
   python main_script.py --config eplprediction/config/config.yaml
   ```

4. **prediction.py**: This script uses a probability estimator network to predict outcomes in the specified European football league and visualizes the predictions.
   ```bash
   python prediction.py --config eplprediction/config/config.yaml
   ```

## Configuration

The configuration file `eplprediction/config/config.yaml` contains various settings for the project, including league information, model parameters, bettor settings, and data gathering paths. You can modify this file to change the configuration according to your needs.

## Data Preprocessing, Model Training, Prediction, and Visualization

### Data Preprocessing

The data preprocessing is handled by the `Preprocessor` class in `eplprediction/data/preprocessor.py`. It includes functions for producing match IDs, normalizing statistics, and handling null values.

### Model Training

The model training is performed by the `FootballPredictor` and `ProbabilityEstimatorNetwork` classes in `eplprediction/models/probability_estimator.py`. These classes handle the training of the regressors and the prediction of match outcomes.

#### Model Details

- The prediction engine uses a two-branch setup inside `ProbabilityEstimatorNetwork`:
  - one branch trained on **short-term form**
  - one branch trained on **long-term form**
- Each branch is a `FootballPredictor` with two regressors:
  - a home-goal regressor
  - an away-goal regressor
- Regressor type is configurable (`LinearRegression`, `PoissonRegressor`, `SVR`) from `eplprediction/config/config.yaml` (default is `PoissonRegressor`).
- The model learns from team form features (home and away stats), and excludes early-season matchdays using `matchdays_to_drop` to reduce unstable early-sample noise.
- Final expected goal rates are blended with weighted voting between long-term and short-term branches (configured via `model.voting`).
- Scoreline probabilities are generated with a Poisson distribution and then aggregated into betting-style probabilities such as:
  - Home/Draw/Away
  - Over/Under (1.5, 2.5, 3.5, 4.5)
  - GG/NG (both teams to score / not)

### Prediction

The prediction of match outcomes is done using the `ProbabilityEstimatorNetwork` class. The predicted probabilities are stored in a dataframe and can be used for further analysis.

#### TL;DR: Prediction Output

- The model outputs **probabilities**, not guaranteed exact scores.
- For each fixture, it returns:
  - scoreline probability distribution (e.g., 0-0, 1-0, 2-1, etc.)
  - 1X2 probabilities (home win / draw / away win)
  - totals and GG/NG probabilities
- Outputs are written to:
  - `Predictions/EPL/PredictionTable.html`
  - `Predictions/EPL/InteractiveFigure.html`

### Visualization

The visualization of predictions is handled by the `Visualizer` class in `eplprediction/viz/vizualization.py`. It includes functions for creating radar plots and scoreline bar plots to visualize the predicted probabilities.

## Repository Structure

- `app.py`: Initializes a Gradio app interface for running the model.
- `historicaldata_collection.py`: Collects historical data and preprocesses it.
- `main_script.py`: Performs data scraping and processing to predict outcomes in the specified English Premier League configuration.
- `prediction.py`: Uses a probability estimator network to predict outcomes in the specified European football league and visualizes the predictions.
- `eplprediction/config/config.yaml`: Configuration file for the project.
- `eplprediction/database/EPL_database.db`: SQLite database file for managing data.
- `eplprediction/data/preprocessor.py`: Handles data preprocessing.
- `eplprediction/models/probability_estimator.py`: Contains classes for model training and prediction.
- `eplprediction/viz/vizualization.py`: Handles visualization of predictions.
- `requirements.txt`: List of dependencies for the project.

# SAVING-PLOTS
This code helps to save plots produced in a notebook
#  How can I save a plot produced in a notebook?
This is a general approach to save the plot with this dataset "acw_user_data.csv"


import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
import os

# Define file paths
CSV_FILE_PATH = "acw_user_data.csv"
PLOTS_DIR = "plots"

# Ensure the plots directory exists
os.makedirs(PLOTS_DIR, exist_ok=True)

# Function to load CSV file
def load_csv(file_path):
    if not os.path.exists(file_path):
        print(f"Error: File '{file_path}' not found. Please check the path.")
        return None
    try:
        return pd.read_csv(file_path)
    except pd.errors.EmptyDataError:
        print("Error: The CSV file is empty.")
    except pd.errors.ParserError as e:
        print(f"Error: {e}")

# Function to clean 'Dependants' column
def clean_dependants_column(df):
    if 'Dependants' in df.columns:
        df['Dependants'] = pd.to_numeric(df['Dependants'], errors='coerce').fillna(0)
    return df

# Function to validate required columns
def validate_columns(df, required_columns):
    missing = [col for col in required_columns if col not in df.columns]
    if missing:
        print(f"Error: Missing columns {missing}")
        return False
    return True

# Function to plot and save age distribution
def plot_age_distribution(df):
    if 'Age (Years)' not in df.columns:
        print("Error: 'Age (Years)' column is missing.")
        return
    plt.figure(figsize=(10, 6))
    sns.histplot(df['Age (Years)'], bins=10, kde=True, color="royalblue", edgecolor="black", alpha=0.7)
    plt.title("Age Distribution of Customers", fontsize=14, fontweight='bold')
    plt.xlabel("Age (Years)", fontsize=12)
    plt.ylabel("Count", fontsize=12)
    plt.savefig(os.path.join(PLOTS_DIR, "age_distribution.png"), dpi=300, bbox_inches='tight')
    plt.show()

# Function to plot and save dependants distribution
def plot_dependants_distribution(df):
    if 'Dependants' not in df.columns:
        print("Error: 'Dependants' column is missing.")
        return
    plt.figure(figsize=(10, 6))
    sns.countplot(data=df, x='Dependants', edgecolor="black")
    plt.title("Distribution of Dependants", fontsize=14, fontweight='bold')
    plt.xlabel("Number of Dependants", fontsize=12)
    plt.ylabel("Count", fontsize=12)
    plt.savefig(os.path.join(PLOTS_DIR, "dependants_distribution.png"), dpi=300, bbox_inches='tight')
    plt.show()

# Function to plot and save age distribution by marital status
def plot_age_distribution_by_marital_status(df):
    required_columns = ['Age (Years)', 'Marital Status']
    if not validate_columns(df, required_columns):
        return
    plt.figure(figsize=(10, 6))
    sns.histplot(data=df, x='Age (Years)', hue='Marital Status', element='step', palette="Set2", edgecolor="black")
    plt.title("Age Distribution by Marital Status", fontsize=14, fontweight='bold')
    plt.xlabel("Age (Years)", fontsize=12)
    plt.ylabel("Count", fontsize=12)
    plt.savefig(os.path.join(PLOTS_DIR, "age_distribution_by_marital_status.png"), dpi=300, bbox_inches='tight')
    plt.show()

# Function to plot and save scatter plots
def plot_scatter_plots(df):
    required_columns = {'Distance Commuted to Work (Km)', 'Yearly Salary (Dollar)', 'Age (Years)', 'Dependants'}
    if not required_columns.issubset(df.columns):
        print(f"Error: Missing one or more required columns: {required_columns}")
        return

    sns.set_style("whitegrid")
    fig, axes = plt.subplots(1, 3, figsize=(18, 6))

    sns.scatterplot(data=df, x='Distance Commuted to Work (Km)', y='Yearly Salary (Dollar)',
                    hue='Yearly Salary (Dollar)', size='Distance Commuted to Work (Km)', sizes=(20, 200),
                    palette="coolwarm", edgecolor="black", alpha=0.75, ax=axes[0])
    sns.regplot(data=df, x='Distance Commuted to Work (Km)', y='Yearly Salary (Dollar)', scatter=False,
                color="black", line_kws={"linewidth": 1.5, "linestyle": "--"}, ax=axes[0])
    axes[0].set_title("Distance Commuted vs Salary")

    sns.scatterplot(data=df, x='Age (Years)', y='Yearly Salary (Dollar)',
                    hue='Yearly Salary (Dollar)', size='Age (Years)', sizes=(20, 200),
                    palette="coolwarm", edgecolor="black", alpha=0.75, ax=axes[1])
    sns.regplot(data=df, x='Age (Years)', y='Yearly Salary (Dollar)', scatter=False,
                color="black", line_kws={"linewidth": 1.5, "linestyle": "--"}, ax=axes[1])
    axes[1].set_title("Age vs Salary")

    sns.scatterplot(data=df, x='Age (Years)', y='Yearly Salary (Dollar)',
                    hue='Dependants', size='Dependants', sizes=(20, 200),
                    palette="viridis", edgecolor="black", alpha=0.75, ax=axes[2])
    sns.regplot(data=df, x='Age (Years)', y='Yearly Salary (Dollar)', scatter=False,
                color="black", line_kws={"linewidth": 1.5, "linestyle": "--"}, ax=axes[2])
    axes[2].set_title("Age vs Salary (Conditioned by Dependants)")

    plt.tight_layout()
    plt.savefig(os.path.join(PLOTS_DIR, "combined_scatter_plots.png"), dpi=300, bbox_inches='tight')
    plt.show()

# Main function to generate all plots
def main():
    df = load_csv(CSV_FILE_PATH)
    if df is not None:
        plot_age_distribution(df)
        df = clean_dependants_column(df)
        plot_dependants_distribution(df)
        plot_age_distribution_by_marital_status(df)
        plot_scatter_plots(df)

# Execute main function
if __name__ == "__main__":
    main()

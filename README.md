# Hello-world
Learning Github
I am learning about branches and commit
import pandas as pd

tax_keywords = ['HST', 'GST', 'QST']

def replace_tax_descriptions(group):
    # Sort transactions by amount in descending order
    sorted_group = group.sort_values(by='Amount', ascending=False)
    
    # Filter for non-tax transactions
    non_tax_rows = sorted_group[~sorted_group['Description'].str.contains('|'.join(tax_keywords), case=False, na=False)]
    non_tax_descriptions = non_tax_rows['Description'].tolist()
    
    # Only proceed if there are non-tax descriptions
    if non_tax_descriptions:
        # Identify tax rows
        tax_rows = sorted_group[sorted_group['Description'].str.contains('|'.join(tax_keywords), case=False, na=False)]
        for idx, tax_row in tax_rows.iterrows():
            # Replace tax description with the first available non-tax description and mark as updated
            transactions_df.at[idx, 'Updated Description'] = non_tax_descriptions.pop(0)
            transactions_df.at[idx, 'Update Flag'] = 1
    else:
        # If no non-tax descriptions are available, leave as is and do not update
        tax_rows = sorted_group[sorted_group['Description'].str.contains('|'.join(tax_keywords), case=False, na=False)]
        for idx, tax_row in tax_rows.iterrows():
            transactions_df.at[idx, 'Updated Description'] = transactions_df.at[idx, 'Description']
            transactions_df.at[idx, 'Update Flag'] = 0

# Group by Date and Employee Name, then apply the replacement function
grouped = transactions_df.groupby(['Date', 'Employee Name'])
grouped.apply(replace_tax_descriptions)


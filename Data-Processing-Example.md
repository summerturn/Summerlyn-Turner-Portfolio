## Data Processing Example

The code below’s main function is to read a KML file exported from EsDAT with chemistry databoxes and processes the data into an Excel sheet with styled and complete databoxes that can be copied into an ArcMap document as is. The script also offers to convert the KML into a Shapefile format to make the process even more streamlined if the locations don’t already exist within a project. Creating databoxes used to be a manual process from front to back and this script now allows the GIS team at my company to automate this portion of the databox creation. This saves us time and energy.

Here is the databoxes as they appear TRAPPED in the ESDAT web interface.
![DataboxExample1](https://github.com/summerturn/Summerlyn-Turner-Portfolio/assets/55452231/16c80aef-2ad4-4ec9-9943-16282e408cd1)

Here are the formatted databoxes in an excel sheet, ready to be pasted into an mxd or further edited! I created a Horizontal and Vertical variation that the team can choose between depding on need.

![DataboxExample3](https://github.com/summerturn/Summerlyn-Turner-Portfolio/assets/55452231/542bd3b5-f0d0-42d8-9f6a-9fd88beb7882)

![DataboxExample2](https://github.com/summerturn/Summerlyn-Turner-Portfolio/assets/55452231/e0fe8dcf-88a0-4735-b3d9-5a8e740c48b1)

### Code

```ruby

from osgeo import ogr, osr
from bs4 import BeautifulSoup
from openpyxl import Workbook
from openpyxl.styles import PatternFill, Font, Alignment, Border, Side
from openpyxl.utils import get_column_letter
from shapely.geometry import shape
import subprocess


def parse_color_value(color_str):
    # Parse the color value and convert it to aRGB hex format
    if color_str.startswith('#'):
        return color_str[1:]
    if color_str.startswith('rgb'):
        rgb_values = color_str[color_str.index('(') + 1:color_str.index(')')].split(',')
        r, g, b = [int(value.strip()) for value in rgb_values]
        return f'{r:02x}{g:02x}{b:02x}'
    return None


def process_kml_file(input_file, output_file):
    with open(input_file, 'r') as f:
        soup = BeautifulSoup(f, 'html.parser')

    wb = Workbook()

    for placemark, description in zip(soup.find_all('name'), soup.find_all('description')):
        placemark_name = placemark.get_text(strip=True)
        ws = wb.create_sheet(title=placemark_name)

        if description:
            cdata_soup = BeautifulSoup(description.string, 'html.parser')
            table = cdata_soup.find('table')

            if table:
                # Get table dimensions
                num_cols = len(table.find_all('tr'))
                num_rows = max(len(row.find_all(['td', 'th'])) for row in table.find_all('tr'))
                
                # Add missing headers by duplicating an existing header from the back
                headers = table.find_all('th')
                missing_headers = num_rows - len(headers)
                header_index = len(headers) - 1  # Start at the last header
                for idx in range(missing_headers):
                    missing_header = headers[header_index]  # Get the header
                    new_header = cdata_soup.new_tag('th')
                    new_header.attrs = missing_header.attrs
                    new_header.string = missing_header.string
                    table.tr.insert(header_index + 1, new_header)
                    header_index -= 1  # Move to the previous header
                            
                # Merge and center the first row
                merged_cell = ws.cell(row=1, column=1, value=placemark_name)
                ws.merge_cells(start_row=1, start_column=1, end_row=1, end_column=num_cols)
                merged_cell.alignment = Alignment(horizontal='center')

                # Set white background for each cell within the merged range
                for row in ws.iter_rows(min_row=1, max_row=1, min_col=1, max_col=num_cols):
                    for cell in row:
                        cell.fill = PatternFill(start_color="FFFFFFFF", end_color="FFFFFFFF", fill_type="solid")
                        cell.border = Border(left=Side(style='thin'), right=Side(style='thin'),
                                             top=Side(style='thin'), bottom=Side(style='thin'))

                # Populate the table data
                col_index = 1
                for row in table.find_all('tr'):
                    row_index = 2  # Start at the second row
                    for idx, cell in enumerate(row.find_all(['td', 'th'])):
                        cell_value = cell.get_text(strip=True)
                        col_letter = get_column_letter(col_index)

                        # Add hyphen if cell value is empty
                        if not cell_value:
                            cell_value = '-'
                        
                        # Check if cell value is a number
                        try:
                            cell_value = float(cell_value)
                        except ValueError:
                            pass
                        
                        ws.cell(row=row_index, column=col_index, value=cell_value)
                        
                        ws[f'{col_letter}{row_index}'] = cell_value

                        # Clear existing formatting and set white background for the cell
                        ws[f'{col_letter}{row_index}'].fill = PatternFill(start_color="FFFFFFFF", end_color="FFFFFFFF", fill_type="solid")
                        ws[f'{col_letter}{row_index}'].border = Border(left=Side(style='thin'), right=Side(style='thin'),
                                             top=Side(style='thin'), bottom=Side(style='thin'))

                        # Apply formatting based on HTML tags or attributes
                        if cell.get('style'):
                            style = cell['style']
                            font = Font()  # Create a new Font object

                            if 'fill' in style:
                                bg_color = style.split('fill:')[-1].split(';')[0].strip()
                                bg_color = parse_color_value(bg_color)
                                if bg_color:
                                    ws[f'{col_letter}{row_index}'].fill = PatternFill(start_color=bg_color,
                                                                                      end_color=bg_color,
                                                                                      fill_type="solid")

                            if 'background' in style:
                                bg_color = style.split('background:')[-1].split(';')[0].strip()
                                bg_color = parse_color_value(bg_color)
                                if bg_color:
                                    ws[f'{col_letter}{row_index}'].fill = PatternFill(start_color=bg_color,
                                                                                      end_color=bg_color,
                                                                                      fill_type="solid")

                            if 'text-align: center' in style:
                                ws[f'{col_letter}{row_index}'].alignment = Alignment(horizontal='center')

                            if 'margin-right:' in style:
                                margin = int(style.split('margin-right:')[-1].split('px')[0].strip())
                                ws.column_dimensions[col_letter].width = 5 + margin
                            if 'padding:' in style:
                                padding = int(style.split('padding:')[-1].split('px')[0].strip())
                                ws.column_dimensions[col_letter].width += padding

                            if 'font-style: italic' in style:
                                font.italic = True  # Set italic property of the Font object
                            if 'font-weight: bold' in style:
                                font.bold = True  # Set bold property of the Font object
                            if 'color' in style:
                                font_color = style.split('color:')[-1].split(';')[0].strip()
                                font_color = parse_color_value(font_color)
                                if font_color:
                                    font.color = font_color  # Set color property of the Font object

                            ws[f'{col_letter}{row_index}'].font = font  # Assign the Font object to the cell

                        row_index += 1  # Increment row index

                    col_index += 1  # Increment column index

    # Remove the default sheet created by openpyxl
    wb.remove(wb['Sheet'])

    # Save the workbook
    wb.save(output_file)

def convert_kml_to_shapefile(input_kml):
    response = input("Do you want to convert the KML file to a shapefile? (Y/N): ")
    if response.lower() == 'y':
        output_directory = input("Enter the directory path to save the output shapefile: ")
        output_shapefile = f"{output_directory}/output_shapefile.shp"

        try:
            # Convert KML to Shapefile using ogr2ogr command-line tool
            subprocess.run(["ogr2ogr", "-f", "ESRI Shapefile", output_shapefile, input_kml])

            print("Shapefile conversion completed successfully.")
        except Exception as e:
            print(f"An error occurred during shapefile conversion: {e}")
    else:
        print("Shapefile conversion skipped.")

input_kml = 'C:/Users/Summer/Downloads/kml_test.kml'
output_filepath = 'C:/Users/Summer/Downloads/test_vert.xlsx'

# Process the KML file and convert it to an Excel file
process_kml_file(input_kml, output_filepath)

# Convert the KML file to a shapefile
convert_kml_to_shapefile(input_kml)

```

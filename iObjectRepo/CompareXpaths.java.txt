package AutoHeal;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.Map;

import org.apache.poi.ss.usermodel.Cell;
import org.apache.poi.ss.usermodel.CellStyle;
import org.apache.poi.ss.usermodel.FillPatternType;
import org.apache.poi.ss.usermodel.Font;
import org.apache.poi.ss.usermodel.IndexedColors;
import org.apache.poi.ss.usermodel.Row;
import org.apache.poi.xssf.usermodel.XSSFCell;
import org.apache.poi.xssf.usermodel.XSSFRow;
import org.apache.poi.xssf.usermodel.XSSFSheet;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;
import org.apache.xmlbeans.impl.xb.xsdschema.FieldDocument.Field.Xpath;

class Compare{
	String excelName = "D:\\xpathGenerator.xlsx";
	void fetchXpaths() {
		try {
			File excelFile = new File(excelName);
		    FileInputStream fis = new FileInputStream(excelFile);
		    XSSFWorkbook wb = new XSSFWorkbook(fis);
		    XSSFSheet sheet = wb.getSheetAt(0);
		    String cell2ValueMaybeNull = "";
		    String cell1ValueMaybeNull = "";
		    Map<Integer, String> userXpath = new HashMap<Integer, String>();
		    Map<Integer, String> generatedXpath = new HashMap<Integer, String>();
		    Row row;
			for (int rowIndex = 1; rowIndex <= sheet.getLastRowNum(); rowIndex++) {
				  row = sheet.getRow(rowIndex);
				  if (row != null ) {
				    Cell cell2 = row.getCell(3);
				    Cell cell1 = row.getCell(2);
				    if (cell2 != null) {
				      cell2ValueMaybeNull = cell2.getStringCellValue();
				      generatedXpath.put(rowIndex, cell2ValueMaybeNull); 
				    }
				    if(cell1 != null) {
				    	cell1ValueMaybeNull = cell1.getStringCellValue();
				    	userXpath.put(rowIndex, cell1ValueMaybeNull);
				    }
				 }
			}	
			compareXpaths(sheet, wb, userXpath, generatedXpath);
		}catch (Exception e) {
			e.printStackTrace();
		}
	}
	void compareXpaths(XSSFSheet sheet, XSSFWorkbook wb, Map<Integer, String> userXpaths, Map<Integer, String> generateXpaths) {
		try {

			int flag = 0;
			for(Map.Entry<Integer, String> pair : userXpaths.entrySet()) {
				String xpath = pair.getValue();
				
				for(Map.Entry<Integer, String> pair1 : generateXpaths.entrySet()) {
					String generateXpath = pair1.getValue();
					if(xpath.equals(generateXpath)) {
						System.out.println("User Xpath===>"+pair.getKey()+"Generated cpath====>"+pair1.getKey());
						XSSFRow r1 = sheet.getRow(pair.getKey());
						CellStyle style = wb.createCellStyle();
						XSSFCell cell1 = r1.createCell(4);
						style.setFillBackgroundColor(IndexedColors.GREEN.getIndex());
						style.setFillPattern(FillPatternType.FINE_DOTS);
						cell1.setCellValue("Matched");
						Font font = wb.createFont();
						font.setColor(IndexedColors.WHITE.getIndex());
						style.setFont(font);
						cell1.setCellStyle(style);
						XSSFRow r2 = sheet.getRow(pair1.getKey());
						XSSFCell cell2 = r2.getCell(3);
						style.setFillBackgroundColor(IndexedColors.GREEN.getIndex());
						style.setFillPattern(FillPatternType.FINE_DOTS);
////						cell2.setCellValue(pair1.getValue());
						cell2.setCellStyle(style);
						flag = 1;
					}
				}
				System.out.println(flag);
				if(flag == 0) {
					System.out.println("hi");
					XSSFRow r1 = sheet.getRow(pair.getKey());
					CellStyle style = wb.createCellStyle();
					XSSFCell cell1 = r1.createCell(4);
					style.setFillBackgroundColor(IndexedColors.RED.getIndex());
					style.setFillPattern(FillPatternType.FINE_DOTS);
					cell1.setCellValue("Not Matched");
					Font font = wb.createFont();
					font.setColor(IndexedColors.WHITE.getIndex());
					style.setFont(font);
					cell1.setCellStyle(style);
				}
				
				
				flag = 0;
				FileOutputStream fileOut = new FileOutputStream(excelName);	
				wb.write(fileOut);
				fileOut.close();
			}
		}catch (Exception e) {
			e.getMessage();
		}
	}	
}
public class CompareXpaths {
	public static void main(String[] args) {
		Compare c = new Compare();
		c.fetchXpaths();
	}
}

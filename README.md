# Recursive-Hexadoku-solver
package Hexadoku;

import java.applet.Applet;
import java.awt.Button;
import java.awt.Color;
import java.awt.GridLayout;
import java.io.IOException;
import java.nio.file.Paths;
import java.util.Scanner;

public class HexadoSolver extends Applet implements Runnable {
	
	private final int row = 16;	//The row    of the sudoku
	private final int col = 16;	//The column of the sudoku
	protected int Sudoku[][];	//The array is used to store the sudoku.
	
	protected Button view[][];
	
	protected int RGB[][] = {
			//This array is used to defined the color value for each number.
			{0,		0, 	 0},//0,this color is the background color of the button.
			{255,	0,	 0},//1
			{0,	  255,   0},//2
			{0,		0, 255},//3
			{255, 100, 	 0},//4
			{255, 255,   0},//5
			{  0, 255, 100},//6
			{  0, 255, 255},//7
			{100,   0, 255},//8
			{200,   0, 255},//9
			{255, 100, 255},//A
			{100, 100, 255},//B
			{255, 255, 100},//C
			{100, 255, 100},//D
			{100, 255, 255},//E
			{150, 150, 150},//F
			{255, 255, 255} //G
	};
	
	private final String fileName = "Hexadukos.txt";	//Hexaduko should be read from the text file.
	
	/**
	 * Initialize the Sudoku with zero
	 */
	private void initSudoku(){
		Sudoku = new int[this.row][this.col];
		for(int i = 0; i < this.row; i++){
			for(int j = 0; j < this.col; j++){
				Sudoku[i][j] = 0;
			}
		}
	}
	
	/**
	 * Load values from a text file
	 * @param FilePath
	 * @throws IOException 
	 */
	private void loadSudoku(String FilePath) throws IOException{
		Scanner inFile = new Scanner(Paths.get(FilePath));
		
		//begin to load the values from the text file
		while(inFile.hasNext()){
			int row = inFile.nextInt();
			int col = inFile.nextInt();
			int value = inFile.nextInt();
			
			Sudoku[row][col] = value;
		}
		
		inFile.close();
	}
	
	/**
	 * create a sudoku and its values are from the given text file.
	 */
	protected void createSudoku(){
		try{
			initSudoku();
			loadSudoku(this.fileName);
		}catch(IOException e){
			e.printStackTrace();
		}
	}
	
	/**
	 * create a empty view
	 */
	protected void createView(){
		setLayout( new GridLayout(this.row,this.col));
		
		view = new Button[this.row][this.col];
		
		for(int i = 0; i < this.row; i++){
			for(int j = 0; j < this.col; j++){
				view[i][j] = new Button();
				add(view[i][j]);
			}
		}
		
		Color OriginColor = view[0][0].getBackground();
		RGB[0][0] = OriginColor.getRed();
		RGB[0][1] = OriginColor.getGreen();
		RGB[0][2] = OriginColor.getBlue();
	}
	
	/**
	 * update the view from the sudoku
	 */
	protected void updateView(){
		for(int i = 0; i < this.row; i++){
			for(int j = 0; j < this.col; j++){
				view[i][j].setBackground(new Color(RGB[Sudoku[i][j]][0],RGB[Sudoku[i][j]][1],RGB[Sudoku[i][j]][2]));
				if(Sudoku[i][j] != 0){
					if(Sudoku[i][j] == 16){
						view[i][j].setLabel("G");
					}
					else{
						view[i][j].setLabel(Integer.toHexString(Sudoku[i][j]).toUpperCase());
					}
				}
				else{
					view[i][j].setLabel("");
				}
			}
		}
	}
	
	/**
	 * Checks whether the number is an acceptable value for given row or not
	 * @param row
	 * @param num
	 * @return  If the number is acceptable, it will return true. Otherwise,it will return false;
	 */
	protected boolean checkRow(int row, int num){
		for(int j = 0; j < this.col; j++){
			if(Sudoku[row][j] == num){
				return false;
			}
		}
		return true;
	}
	
	/**
	 * Checks whether the number is an acceptable value for given column or not 
	 * @param col
	 * @param num
	 * @return
	 */
	protected boolean checkCol(int col,int num){
		for(int i = 0; i < this.row; i++){
			if(Sudoku[i][col] == num){
				return false;
			}
		}
		return true;
	}
	
	/**
	 * Check whether the number is an acceptable value for the box around row and column
	 * @param row
	 * @param col
	 * @param num
	 * @return
	 */
	protected boolean checkBox(int row, int col,int num){
		row = (row/4) * 4;
		col = (col/4) * 4;
		
		for(int r = 0; r < 4; r++){
			for(int c = 0; c < 4; c++){
				if(Sudoku[row+r][col+c] == num){
					return false;
				}
			}
		}
		return true;
	}
	
	/**
	 * The method uses a recursive backtrack algorithm to solve the Hexadoku column by column 
	 * @param row
	 * @param col
	 * @return
	 * @throws Exception
	 */
	public boolean solve(int row,int col) throws Exception{
		
		if(row >= this.row){
			row = 0;
			col++;				//It solves the Hexadoku column by column instead of row by row.
			if(col >= this.col){
				return true;
			}
		}
		
		
		if(Sudoku[row][col] != 0){
			return solve(row+1,col);
		}
		else{
			for(int num = 1; num <= this.row; num++){
				if(checkRow(row,num) && checkCol(col,num) && checkBox(row,col,num)){
					Sudoku[row][col] = num;
					updateView();
					
					if(solve(row+1,col)){
						return true;
					}
					Sudoku[row][col] = 0;
					updateView();
				}
			}
		}
		
		return false;
	}
	
	/**
	 * This method is called by the browser when the applet is loaded 
	 */
	public void init(){
		createSudoku();
		createView();
		updateView();
	}
	
	public void start(){
		(new Thread(this)).start();
	}
	@Override
	public void run() {
		// TODO Auto-generated method stub
		try{
			solve(0,0);
		}catch(Exception e){
			
		}
	}

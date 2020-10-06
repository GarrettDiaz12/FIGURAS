# FIGURAS
Programa que dibuja un linea,rectangulo,ovalo con rellEno de color o sin relleno
PAINT/DIAZ CERVERA BRIA NOE / ITL / TAP / 2020
Desarrollar un programa que implemente una aplicación con GUI de dibujo interactivo.
La aplicación puede dibujar lineas, óvalos y rectángulos, pudiendo seleccionar colores y poder dibujar figuras rellenas. 
Debe existir un botón para deshacer la última figura que se haya dibujado.
Un botón para borrar todas las figuras del dibujo.
Un botón para seleccionar el color. Un cuadro combinado para seleccionar la figura a dibujar. 
Una casilla de verificación que especifique si una figura debe estar rellena o sin relleno.
-------------------------------------------------------------------------------------------------------------------------------------------
                                                               LIBRERIAS UTILIZADAS
------------------------------------------------------------------------------------------------------------------------------------------- 

import java.awt.BorderLayout;
	import java.awt.Color;
	import java.awt.Dimension;
	import java.awt.Graphics;
	import java.util.Stack;
	import java.awt.event.ActionListener;
	import java.awt.event.ActionEvent;
	import java.awt.event.MouseAdapter;
	import java.awt.event.MouseMotionAdapter;
	import java.awt.event.MouseEvent;
	import javax.swing.JButton;
	import javax.swing.JCheckBox;
	import javax.swing.JComboBox;
	import javax.swing.JFrame;
	import javax.swing.JLabel;
	import javax.swing.JPanel;
  ------------------------------------------------------------------------------------------------------------------------------------------ 
En esta clase es donde se crear los botones para poder poner su tamaño también se crea como tal la interfaz y los métodos los cuales nos 
permiten hacer las acciones de dibujar las figuras, rellenar de color las figuras, dejarlas sin relleno como el tipo de figura que se creara 
y los métodos de eliminar la última figura como todas las figuras ya que para poder hacer el método de eliminar una imagen antes se utilizó la 
ayuda de una pila para facilitar el procedimiento como para así saber cuál fue la última figura creada como tal pero los métodos más importantes 
para mi punto de vista fue la implementación de mouseReleased, MouseMotionListener, mouseDragged ya esto nos permite dibujar las figuras en cualquiera
parte de la ventana con ayuda del mouse y las coordenadas ya que nos ayuda mucho para al momento de hacer es de demasiada ayuda para poder hacer click 
arrastra el mouse hasta donde queremos dibujar la figura y hasta soltar se accionara para que se cree la figura y al soltar el se pueda dibujar de manera 
satisfactoria  de igual amnera utlize un arreglo para la el combox asi asignarle la posision de etique para la figura como para los colores

------------------------------------------------------------------------------------------------------------------------------------------ 

	package figurasapps;
	import java.awt.BorderLayout;
	import java.awt.Color;
	import java.awt.Dimension;
	import java.awt.Graphics;
	import java.util.Stack;
	import java.awt.event.ActionListener;
	import java.awt.event.ActionEvent;
	import java.awt.event.MouseAdapter;
	import java.awt.event.MouseMotionAdapter;
	import java.awt.event.MouseEvent;
	import javax.swing.JButton;
	import javax.swing.JCheckBox;
	import javax.swing.JComboBox;
	import javax.swing.JFrame;
	import javax.swing.JLabel;
	import javax.swing.JPanel;
	@SuppressWarnings("serial")
	public class Paint extends JFrame {
	  private final int UPDATE_RATE = 60;
	  private int width;
	  private int height;
	  private PanelCanvas canvas;
	  private JPanel mainPanel, toolsPanel;
	  private JLabel lblMousePosition;
	  private JButton btnUnDo, btnClear;
	  private JComboBox<String> coBoxColor, coBoxShape;
	  private JCheckBox chBoxIsFilled;
	  private Stack<PaintableShape> shapes;
	  private int startX, startY;
	  private int endX, endY;
	  private boolean isDrawing = false;
	  public Paint(int width, int height) {
	    this.width = width;
	    this.height = height;
	    this.canvas = new PanelCanvas();
	    this.mainPanel = new JPanel(new BorderLayout());
	    this.toolsPanel = new JPanel();
	    this.lblMousePosition = new JLabel("0, 0");
	    this.btnUnDo = new JButton("Undo");
	    this.btnClear = new JButton("Clear");
	    this.coBoxColor = new JComboBox<String>(new String[] { "RED", "GREEN", "BLUE", "CYAN", "MAGENTA", "YELLOW","BLACK","ORANGE","PINK","DARCK GREY","GRAY","SUNRAY",
	        "LAPIS LAZULI","DARCK BLUE GRAY","LIGTH BLUE","CELADON","SPRING GREEN","MYSTIC MAROON"});
	    
	    this.coBoxShape = new JComboBox<String>(new String[] { "RECTANGLE", "ELLIPSE", "LINE" });
	    this.chBoxIsFilled = new JCheckBox("Filled");
	

	    shapes = new Stack<PaintableShape>();
	

	    addAttributes();
	    addListeners();
	    build();
	

	    this.pack();
	    this.setLocationRelativeTo(null);
	

	    startLoop();
	  }
	

	  public void addAttributes() {
	    this.setTitle("FIGURES/TOPICOS/ITL/DIAZ CERVERA BRIAN NOE");
	    this.setVisible(true);
	    this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
	    this.setResizable(false);
	  }
	

	  public void addListeners() {
	    // Undo button.
	    btnUnDo.addActionListener(new ActionListener() {
	      public void actionPerformed(ActionEvent e) {
	        if (shapes.size() > 0)
	          shapes.pop();
	      }
	    });
	

	    // Clear button.
	    btnClear.addActionListener(new ActionListener() {
	      public void actionPerformed(ActionEvent e) {
	        if (shapes.size() > 0)
	          shapes.clear();
	      }
	    });
	

	    // Canvas mouse coordinates.
	    canvas.addMouseListener(new MouseAdapter() {
	      public void mousePressed(MouseEvent e) {
	        startX = e.getX();
	        startY = e.getY();
	

	        isDrawing = true;
	      }
	

	      public void mouseReleased(MouseEvent e) {
	        shapes.push(new PaintableShape(startX, startY, endX, endY, coBoxShape.getSelectedIndex(),
	            chBoxIsFilled.isSelected(), PaintableShape.COLORS[coBoxColor.getSelectedIndex()]));
	

	        isDrawing = false;
	      }
	    });

	    canvas.addMouseMotionListener(new MouseMotionAdapter() {
	      public void mouseMoved(MouseEvent e) {
	        lblMousePosition.setText(e.getX() + ", " + e.getY());
	      }
	      public void mouseDragged(MouseEvent e) {
	        boolean lineSelected = coBoxShape.getSelectedIndex() == PaintableShape.LINE;
	        endX = (lineSelected) ? e.getX() : e.getX() - startX;
	        endY = (lineSelected) ? e.getY() : e.getY() - startY;
	        lblMousePosition.setText(startX + ", " + startY + "; " + endX + ", " + endY);
	      }
	    });
	  }
	

	  public void build() {
	    this.toolsPanel.add(btnUnDo);
	    this.toolsPanel.add(btnClear);
	    this.toolsPanel.add(coBoxColor);
	    this.toolsPanel.add(coBoxShape);
	    this.toolsPanel.add(chBoxIsFilled);
	    this.mainPanel.add(toolsPanel, BorderLayout.NORTH);
	    this.mainPanel.add(canvas, BorderLayout.CENTER);
	    this.mainPanel.add(lblMousePosition, BorderLayout.SOUTH);
	

	    this.add(mainPanel);
	  }
	

	  public void startLoop() {
	    Thread drawLoop = new Thread() {
	      public void run() {
	        while (true) {
	          repaint();
	

	          try {
	            Thread.sleep(1000 / 60); // Desired frame rate.
	          } catch (InterruptedException ex) {
	          }
	        }
	      }
	    }
	            ;
	

	    drawLoop.start();
	  }
	

	  class PanelCanvas extends JPanel {
	

	    protected void paintComponent(Graphics g) {
	      super.paintComponent(g);
	

	      g.setColor(Color.decode("#F4F7F5"));
	      g.fillRect(0, 0, width, height);
	

	      shapes.stream().forEach(shape -> {
	        g.setColor(shape.getColor());
	

	        switch (shape.getType()) {
	          case PaintableShape.RECTANGLE:
	            if (shape.isFilled())
	              g.fillRect(shape.startX(), shape.startY(), shape.endX(), shape.endY());
	            else
	              g.drawRect(shape.startX(), shape.startY(), shape.endX(), shape.endY());
	            break;
	          case PaintableShape.ELLIPSE:
	            if (shape.isFilled())
	              g.fillOval(shape.startX(), shape.startY(), shape.endX(), shape.endY());
	            else
	              g.drawOval(shape.startX(), shape.startY(), shape.endX(), shape.endY());
	            break;
	          case PaintableShape.LINE:
	            g.drawLine(shape.startX(), shape.startY(), shape.endX(), shape.endY());
	            break;
	        }
	

	      });
	

	      if (isDrawing) {
	        g.setColor(Color.decode("#BBAADD"));
	        switch (coBoxShape.getSelectedIndex()) {
	          case PaintableShape.RECTANGLE:
	            g.drawRect(startX, startY, endX, endY);
	            break;
	          case PaintableShape.ELLIPSE:
	            g.drawOval(startX, startY, endX, endY);
	            break;
	          case PaintableShape.LINE:
	            g.drawLine(startX, startY, endX, endY);
	            break;
	        }
	      }
	    }
	

	    @Override
	    public Dimension getPreferredSize() {
	      return new Dimension(width, height);
	    }
	
	  }
	
	}

-------------------------------------------------------------------------------------------------------------------------------------------
Como tal en esta clase es la encargada de darle las posiciones al arreglo que con ayuda del ComboBox nos mostrara en la 
interface los colores, entonces aqui se implentan distintos colores ya que con la ayuda de de java como se implento algunos 
colores especificos con codigo hexadecimal ya que para esto tenemos que tomar en cuenta la posicion de el comboboc y el arreglo
para que funcione correctamente.
------------------------------------------------------------------------------------------------------------------------------------------
	package figurasapps;
	import java.awt.Color;
	public class PaintableShape {
	
	  public static final int RECTANGLE = 0;
	  public static final int ELLIPSE = 1;
	  public static final int LINE = 2;

	  public static final Color[] COLORS = new Color[] { Color.RED, Color.GREEN, Color.BLUE, Color.CYAN, Color.MAGENTA,
	      Color.YELLOW, Color.BLACK,Color.ORANGE, Color.pink, Color.darkGray,Color.gray, Color.decode("#EDAE49"),Color.decode("#00798C"),Color.decode("#726DA8"),
	      Color.decode("#A0D2DB"),Color.decode("#A6D49F"),Color.decode("#2AFC98"),Color.decode("#AF3B6E")};
	

	  private int type;
	  private boolean isFilled;
	  private Color color;
	

	  private int startX, startY;
	  private int endX, endY;
	

	  public PaintableShape(int startX, int startY, int endX, int endY, int type, boolean isFilled, Color color) {
	

	    this.type = type;
	    this.isFilled = isFilled;
	    this.color = color;
	    this.startX = startX;
	    this.startY = startY;
	    this.endX = endX;
	    this.endY = endY;
	

	  }

	  public int getType() {
	    return this.type;
	  }
	

	  public boolean isFilled() {
	    return this.isFilled;
	  }
	

	  public Color getColor() {
	    return this.color;
	  }
	

	  public int startX() {
	    return this.startX;
	  }
	

	  public int startY() {
	    return this.startY;
	  }
	

	  public int endX() {
	    return this.endX;
	  }	

	  public int endY() {
	    return this.endY;
	  }	

	}

-------------------------------------------------------------------------------------------------------------------------------------------
Esta clase basicamente se encargar de poner a funcionar todas las clases en cinjutno para la correcta ejecuacion y de igual manera se la pasan
dos parametros que son los tamaños de la ventana de la interface que llega a visualizar el usuario
-------------------------------------------------------------------------------------------------------------------------------------------
package figurasapps;
	public class Run {
	    public static void main(String[] args) {
	        new Paint(500, 500);
	    }
	}
----------------------------------------------------------------------------------------------------------------------------------------------  
	

	


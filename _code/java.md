---
title: "My Java program"
excerpt: "My one and only Java program."
header:
  teaser: java.png
sidebar:
  - title: "My Java program"
    image: java.png
    text: "My one and only Java program."
comments: true
---

This is the only Java program I have made to date. I am not a big fan of the language, in fact I much prefer Python or a C language. Nevertheless, Java is still a widely used language and so I will eventually go back to it and write more programs.

## PPINet

This program was designed for Systems Biology, working with nodes and degrees. It inputs a protein-protein interaction text file, constructs a network and can output the degree to any given node, the average degree of all nodes, the **hubs** of the network (nodes of highest degree), and the full degree distribution.

I compiled the program and it runs with a nice simple GUI (as shown the image bellow).

<figure>
	<img src="/images/PPINet.png">
</figure>

However, due to time constraints and my last minute decision to use NetBeans instead of Eclipse to construct the GUI (which admitedly I should have done before writing the actual code...) the code ended up being too long. Instead of 4 classes I had to make 7 because for some reason the code worked perfectly on Eclipse yet NetBeans did not allow me to call the methods from the GUI buttons, so I had to separate them into separate classes. By that time it was too late to ask for help on Stack Overflow. Nevertheless, the program worked so I did not work on it any further.

And so, here is the Network class:

```java
package PPNet2;
import java.io.*;
//import java.net.URL;
import java.util.*;
import java.awt.BorderLayout;
import java.awt.Container;
import java.awt.GridLayout;

import javax.swing.*;
import javax.swing.JFrame;

public class Network extends JFrame {
        // creates array for nodes
	static Node[] listOfNodes = new Node[1000];
	public static int noNodes;
	// creates array for degrees
	private static int[] noDegrees = new int[100];
	// has the GUI prints
	public Network() {
		setTitle("List of nodes with degree");
		Container contents = getContentPane();
		contents.setLayout(new GridLayout(0,1));
		JTextArea jt = new JTextArea(50,50);
		contents.add((new JScrollPane(jt)));
		
		for (int j=0; j<100; j++)
		{
			noDegrees[j]= 0;
		}
		
		int avg = 0;
		int max = -1;
		for (int i=0; i<noNodes; i++)
		{
			int degree = listOfNodes[i].getNoEdges();
			jt.append(listOfNodes[i].getName() + "\t" + degree  + "\n");
			if (degree>max)
				max = degree;
			avg += degree;
			noDegrees[degree]++;
		}		
		//setDefaultCloseOperation(EXIT_ON_CLOSE);
		pack();
	}
	                     
	private static int checkIfExists(String newName)
	{
		for (int i=0; i<noNodes; i++)
		{
			if (listOfNodes[i].getName().compareTo(newName) == 0)
				return i;
		}
		
		return 0;
	}
	
	public static void readFile() throws IOException
	{
		
	// make method that reads files
	Scanner file = new Scanner(new File("PPInet.txt"));
	//PrintWriter writer = new PrintWriter("output.txt");
	while(file.hasNext()){
		String line = file.nextLine();
		
		int k = 0;
		String pro1 = "", pro2 = "";
		for (String temp: line.split("\t"))
		{
			k++;
			
			if (k == 1)
			{
				pro1 = temp;
			}
			else
			{
				pro2 = temp;
			}		
		}		
		int pro1e = checkIfExists(pro1);
		int pro2e = checkIfExists(pro2);
		
		if (pro1e != 0)
		{
			Node pro1n = listOfNodes[pro1e];
			if (pro2e != 0)
			{
				if (pro1n.checkEdge(pro2e) == 0)
					pro1n.addEdge(pro2e);
				
				Node pro2n = listOfNodes[pro2e];
				if (pro2n.checkEdge(pro1e) == 0)
					pro2n.addEdge(pro1e);
			}
			else
			{
				Node pro2n = new Node(pro2, noNodes);
				listOfNodes[noNodes] = pro2n;
				
				pro1n.addEdge(noNodes);
				pro2n.addEdge(pro1e);
				
				noNodes++;
			}
		}
		else
		{
			if (pro2e != 0)
			{
				Node pro1n = new Node(pro1, noNodes);
				listOfNodes[noNodes] = pro1n;
				Node pro2n = listOfNodes[pro2e];
				
				pro2n.addEdge(noNodes);
				pro1n.addEdge(pro2e);
				
				noNodes++;
			}
			else
			{

				Node pro1n = new Node(pro1, noNodes);
				listOfNodes[noNodes] = pro1n;
				noNodes++;
				
				int copy = noNodes;
				Node pro2n = new Node(pro2, copy);
				listOfNodes[noNodes] = pro2n;
				
				pro1n.addEdge(noNodes);
				pro2n.addEdge(noNodes - 1);
				noNodes++;
			}
		}	
	}
	file.close();
	//writer.close();
	}
	
	// The following has the console prints
        // They can be used through the NetworkAnalysis class
        
	/*public static void printList()
	{
		for (int j=0; j<100; j++)
		{
			noDegrees[j]= 0;
		}
		
		int avg = 0;
		int max = -1;
		for (int i=0; i<noNodes; i++)
		{
			int degree = listOfNodes[i].getNoEdges();
			System.out.println(listOfNodes[i].getName() + "\t" + degree);
			if (degree>max)
				max = degree;
			avg += degree;
			noDegrees[degree]++;
		}		
	}
        public static void printAvrg()
        {
            for (int j=0; j<100; j++)
		{
			noDegrees[j]= 0;
		}
		
		int avg = 0;
		int max = -1;
		for (int i=0; i<noNodes; i++)
		{
			int degree = listOfNodes[i].getNoEdges();
			if (degree>max)
				max = degree;
			avg += degree;
			noDegrees[degree]++;
		}
		
		System.out.println("Average number of degrees : " + (double)avg / (double)noNodes);		
        }
        public static void printHighest()
        {
            for (int j=0; j<100; j++)
		{
			noDegrees[j]= 0;
		}
		
		int avg = 0;
		int max = -1;
		for (int i=0; i<noNodes; i++)
		{
			int degree = listOfNodes[i].getNoEdges();
			if (degree>max)
				max = degree;
			avg += degree;
			noDegrees[degree]++;
		}
                System.out.println("Max number of degrees: " + max);
        }
        public static void printDegreeDist()
        {
            for (int j=0; j<100; j++)
		{
			noDegrees[j]= 0;
		}
		
		int avg = 0;
		int max = -1;
		for (int i=0; i<noNodes; i++)
		{
			int degree = listOfNodes[i].getNoEdges();
			if (degree>max)
				max = degree;
			avg += degree;
			noDegrees[degree]++;
		}		
		for (int j=0; j<=max; j++)
		{
                    System.out.println(j + " " + noDegrees[j]);
		}
        }*/
        
}
```

The NetworkAnalysis class, containing all the methods:

```java
package PPNet2;
import java.io.*;

// Contains all the methods
public class NetworkAnalysis {
    
	public static void listAnalysis() throws IOException
	{
		Network.noNodes = 0;
		Network.readFile();
		
		Network na = new Network();
		na.setVisible(true);
		
                // The following are the console prints which are currently deactivated
		//Network.printList();
		//Network.printAvrg();
		//Network.printHighest();
                //Network.printDegreeDist();
	}
        public static void avrgAnalysis() throws IOException
	{
		avrgNetwork.noNodes = 0;
		avrgNetwork.readFile();
		
		avrgNetwork na = new avrgNetwork();
		na.setVisible(true);
	}
        public static void highestAnalysis() throws IOException
	{
		highNetwork.noNodes = 0;
		highNetwork.readFile();
		
		highNetwork na = new highNetwork();
		na.setVisible(true);
	}
        public static void degDistAnalysis() throws IOException
	{
		degDistNetwork.noNodes = 0;
		degDistNetwork.readFile();
		
		degDistNetwork na = new degDistNetwork();
		na.setVisible(true);
	}
}
```

The Node class:

```java
package PPNet2;

//create Node class

public class Node extends Network{
//represents Node of Network
	private String name;
	public int id;
	public int[] edges;
	private int noEdges;
	
	public Node(String newName, int newID) {
	// create default constructor that creates node with empty name
		name = newName;
		id = newID;
		noEdges = 0;
		edges = new int[100];
	}
	
	public void addEdge(int otherID)
	{
		edges[noEdges] = otherID;
		noEdges++;
	}
	
	public int getNoEdges()
	{
		return noEdges;	
	}
	
	public String getName()
	{
		return name;
	}
	
	public int checkEdge(int otherID)
	{
		for (int i=0; i<noEdges; i++)
		{
			if (edges[i] == otherID)
				return 1;
		}
		return 0;
	}
	
}
```

And last but not least, the GUI:

```java
package PPNet2;

import static PPNet2.NetworkAnalysis.avrgAnalysis;
import static PPNet2.NetworkAnalysis.degDistAnalysis;
import static PPNet2.NetworkAnalysis.highestAnalysis;
import static PPNet2.NetworkAnalysis.listAnalysis;
import java.io.IOException;
import java.util.logging.Level;
import java.util.logging.Logger;
import javax.swing.JFrame;
import javax.swing.*;

/**
 * author: Nicolas Pettican
 */
public class NewJFrame extends JFrame {
   
    //Creates new form NewJFrame
    
    public NewJFrame() {
        initComponents();
    }
    
     //This method is called from within the constructor to initialize the form.
    
    @SuppressWarnings("unchecked")
    // <editor-fold defaultstate="collapsed" desc="Generated Code">//GEN-BEGIN:initComponents
    private void initComponents() {

        jPanel1 = new javax.swing.JPanel();
        List = new javax.swing.JButton();
        avrg = new javax.swing.JButton();
        highest = new javax.swing.JButton();
        fulldist = new javax.swing.JButton();
        jLabel1 = new javax.swing.JLabel();
        jLabel2 = new javax.swing.JLabel();
        exit = new javax.swing.JButton();

        setDefaultCloseOperation(javax.swing.WindowConstants.EXIT_ON_CLOSE);

        List.setFont(new java.awt.Font("Arial Black", 0, 24)); // NOI18N
        List.setText("List of Nodes + degree");
        List.addActionListener(new java.awt.event.ActionListener() {
            public void actionPerformed(java.awt.event.ActionEvent evt) {
                ListActionPerformed(evt);
            }
        });

        avrg.setFont(new java.awt.Font("Arial Black", 0, 24)); // NOI18N
        avrg.setText("Average degree number");
        avrg.addActionListener(new java.awt.event.ActionListener() {
            public void actionPerformed(java.awt.event.ActionEvent evt) {
                avrgActionPerformed(evt);
            }
        });

        highest.setFont(new java.awt.Font("Arial Black", 0, 24)); // NOI18N
        highest.setText("Highest degree number");
        highest.addActionListener(new java.awt.event.ActionListener() {
            public void actionPerformed(java.awt.event.ActionEvent evt) {
                highestActionPerformed(evt);
            }
        });

        fulldist.setFont(new java.awt.Font("Arial Black", 0, 24)); // NOI18N
        fulldist.setText("Full degree distribution");
        fulldist.addActionListener(new java.awt.event.ActionListener() {
            public void actionPerformed(java.awt.event.ActionEvent evt) {
                fulldistActionPerformed(evt);
            }
        });

        jLabel1.setFont(new java.awt.Font("Arial Black", 1, 48)); // NOI18N
        jLabel1.setText("PPINet");

        jLabel2.setFont(new java.awt.Font("Arial", 0, 18)); // NOI18N
        jLabel2.setText("Protein - protein interaction network analysis software. Developed by Nicolas Pettican.");

        exit.setFont(new java.awt.Font("Arial Black", 0, 14)); // NOI18N
        exit.setText("Exit");
        exit.addActionListener(new java.awt.event.ActionListener() {
            public void actionPerformed(java.awt.event.ActionEvent evt) {
                exitActionPerformed(evt);
            }
        });

        javax.swing.GroupLayout jPanel1Layout = new javax.swing.GroupLayout(jPanel1);
        jPanel1.setLayout(jPanel1Layout);
        jPanel1Layout.setHorizontalGroup(
            jPanel1Layout.createParallelGroup(javax.swing.GroupLayout.Alignment.LEADING)
            .addGroup(jPanel1Layout.createSequentialGroup()
                .addContainerGap()
                .addGroup(jPanel1Layout.createParallelGroup(javax.swing.GroupLayout.Alignment.LEADING)
                    .addGroup(jPanel1Layout.createSequentialGroup()
                        .addGroup(jPanel1Layout.createParallelGroup(javax.swing.GroupLayout.Alignment.LEADING)
                            .addGroup(javax.swing.GroupLayout.Alignment.TRAILING, jPanel1Layout.createSequentialGroup()
                                .addGroup(jPanel1Layout.createParallelGroup(javax.swing.GroupLayout.Alignment.TRAILING)
                                    .addComponent(highest, javax.swing.GroupLayout.DEFAULT_SIZE, javax.swing.GroupLayout.DEFAULT_SIZE, Short.MAX_VALUE)
                                    .addComponent(List, javax.swing.GroupLayout.DEFAULT_SIZE, javax.swing.GroupLayout.DEFAULT_SIZE, Short.MAX_VALUE))
                                .addGap(18, 18, 18)
                                .addGroup(jPanel1Layout.createParallelGroup(javax.swing.GroupLayout.Alignment.LEADING, false)
                                    .addComponent(avrg, javax.swing.GroupLayout.DEFAULT_SIZE, 406, Short.MAX_VALUE)
                                    .addComponent(fulldist, javax.swing.GroupLayout.DEFAULT_SIZE, javax.swing.GroupLayout.DEFAULT_SIZE, Short.MAX_VALUE)))
                            .addGroup(jPanel1Layout.createSequentialGroup()
                                .addComponent(exit, javax.swing.GroupLayout.PREFERRED_SIZE, 92, javax.swing.GroupLayout.PREFERRED_SIZE)
                                .addGap(0, 0, Short.MAX_VALUE)))
                        .addContainerGap())
                    .addGroup(javax.swing.GroupLayout.Alignment.TRAILING, jPanel1Layout.createSequentialGroup()
                        .addGap(0, 78, Short.MAX_VALUE)
                        .addGroup(jPanel1Layout.createParallelGroup(javax.swing.GroupLayout.Alignment.LEADING)
                            .addGroup(javax.swing.GroupLayout.Alignment.TRAILING, jPanel1Layout.createSequentialGroup()
                                .addComponent(jLabel2, javax.swing.GroupLayout.PREFERRED_SIZE, 695, javax.swing.GroupLayout.PREFERRED_SIZE)
                                .addGap(76, 76, 76))
                            .addGroup(javax.swing.GroupLayout.Alignment.TRAILING, jPanel1Layout.createSequentialGroup()
                                .addComponent(jLabel1)
                                .addGap(328, 328, 328))))))
        );
        jPanel1Layout.setVerticalGroup(
            jPanel1Layout.createParallelGroup(javax.swing.GroupLayout.Alignment.LEADING)
            .addGroup(jPanel1Layout.createSequentialGroup()
                .addGap(21, 21, 21)
                .addComponent(jLabel1, javax.swing.GroupLayout.PREFERRED_SIZE, 73, javax.swing.GroupLayout.PREFERRED_SIZE)
                .addPreferredGap(javax.swing.LayoutStyle.ComponentPlacement.RELATED)
                .addComponent(jLabel2, javax.swing.GroupLayout.PREFERRED_SIZE, 41, javax.swing.GroupLayout.PREFERRED_SIZE)
                .addGap(22, 22, 22)
                .addGroup(jPanel1Layout.createParallelGroup(javax.swing.GroupLayout.Alignment.LEADING, false)
                    .addComponent(List, javax.swing.GroupLayout.DEFAULT_SIZE, 123, Short.MAX_VALUE)
                    .addComponent(avrg, javax.swing.GroupLayout.DEFAULT_SIZE, javax.swing.GroupLayout.DEFAULT_SIZE, Short.MAX_VALUE))
                .addGap(18, 18, 18)
                .addGroup(jPanel1Layout.createParallelGroup(javax.swing.GroupLayout.Alignment.LEADING, false)
                    .addComponent(highest, javax.swing.GroupLayout.DEFAULT_SIZE, 123, Short.MAX_VALUE)
                    .addComponent(fulldist, javax.swing.GroupLayout.DEFAULT_SIZE, javax.swing.GroupLayout.DEFAULT_SIZE, Short.MAX_VALUE))
                .addGap(18, 18, 18)
                .addComponent(exit, javax.swing.GroupLayout.DEFAULT_SIZE, 38, Short.MAX_VALUE)
                .addContainerGap())
        );

        javax.swing.GroupLayout layout = new javax.swing.GroupLayout(getContentPane());
        getContentPane().setLayout(layout);
        layout.setHorizontalGroup(
            layout.createParallelGroup(javax.swing.GroupLayout.Alignment.LEADING)
            .addGroup(layout.createSequentialGroup()
                .addComponent(jPanel1, javax.swing.GroupLayout.PREFERRED_SIZE, javax.swing.GroupLayout.DEFAULT_SIZE, javax.swing.GroupLayout.PREFERRED_SIZE)
                .addGap(0, 0, Short.MAX_VALUE))
        );
        layout.setVerticalGroup(
            layout.createParallelGroup(javax.swing.GroupLayout.Alignment.LEADING)
            .addComponent(jPanel1, javax.swing.GroupLayout.DEFAULT_SIZE, javax.swing.GroupLayout.DEFAULT_SIZE, Short.MAX_VALUE)
        );

        pack();
    }// </editor-fold>//GEN-END:initComponents

    private void ListActionPerformed(java.awt.event.ActionEvent evt) {//GEN-FIRST:event_ListActionPerformed
        try {
            listAnalysis();                   
            } catch (IOException ex) {
            Logger.getLogger(NewJFrame.class.getName()).log(Level.SEVERE, null, ex);
        }
    }//GEN-LAST:event_ListActionPerformed

    private void avrgActionPerformed(java.awt.event.ActionEvent evt) {//GEN-FIRST:event_avrgActionPerformed
        try {

            avrgAnalysis();
        } catch (IOException ex) {
            Logger.getLogger(NewJFrame.class.getName()).log(Level.SEVERE, null, ex);
        }
    }//GEN-LAST:event_avrgActionPerformed

    private void exitActionPerformed(java.awt.event.ActionEvent evt) {//GEN-FIRST:event_exitActionPerformed
        System.exit(0);
    }//GEN-LAST:event_exitActionPerformed

    private void highestActionPerformed(java.awt.event.ActionEvent evt) {//GEN-FIRST:event_highestActionPerformed
    try {
            highestAnalysis();
        } catch (IOException ex) {
            Logger.getLogger(NewJFrame.class.getName()).log(Level.SEVERE, null, ex);
        }       
    }//GEN-LAST:event_highestActionPerformed

    private void fulldistActionPerformed(java.awt.event.ActionEvent evt) {//GEN-FIRST:event_fulldistActionPerformed
        try {
            degDistAnalysis();
        } catch (IOException ex) {
            Logger.getLogger(NewJFrame.class.getName()).log(Level.SEVERE, null, ex);
        }
    }//GEN-LAST:event_fulldistActionPerformed

    // The main method
    public static void main(String args[]) {
        //<editor-fold defaultstate="collapsed" desc=" Look and feel setting code (optional) ">
        try {
            for (javax.swing.UIManager.LookAndFeelInfo info : javax.swing.UIManager.getInstalledLookAndFeels()) {
                if ("Nimbus".equals(info.getName())) {
                    javax.swing.UIManager.setLookAndFeel(info.getClassName());
                    break;
                }
            }
        } catch (ClassNotFoundException ex) {
            java.util.logging.Logger.getLogger(NewJFrame.class.getName()).log(java.util.logging.Level.SEVERE, null, ex);
        } catch (InstantiationException ex) {
            java.util.logging.Logger.getLogger(NewJFrame.class.getName()).log(java.util.logging.Level.SEVERE, null, ex);
        } catch (IllegalAccessException ex) {
            java.util.logging.Logger.getLogger(NewJFrame.class.getName()).log(java.util.logging.Level.SEVERE, null, ex);
        } catch (javax.swing.UnsupportedLookAndFeelException ex) {
            java.util.logging.Logger.getLogger(NewJFrame.class.getName()).log(java.util.logging.Level.SEVERE, null, ex);
        }
        //</editor-fold>

        /* Create and display the form */
        java.awt.EventQueue.invokeLater(() -> {
            new NewJFrame().setVisible(true);
        });
        
    }

    // Variables declaration - do not modify//GEN-BEGIN:variables
    private javax.swing.JButton List;
    private javax.swing.JButton avrg;
    private javax.swing.JButton exit;
    private javax.swing.JButton fulldist;
    private javax.swing.JButton highest;
    private javax.swing.JLabel jLabel1;
    private javax.swing.JLabel jLabel2;
    private javax.swing.JPanel jPanel1;
    // End of variables declaration//GEN-END:variables
}
```

The other classes that output the different calculations differ from the Network class in only a couple of lines under the Network() object.

Feel free to leave a comment if you think of a reason why I was unable to call the methods from the GUI buttons (I'm sure if you're experienced with Java you'll spot it).


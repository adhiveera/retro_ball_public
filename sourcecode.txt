import java.io.*;
import java.lang.*;
import java.awt.*;
import javax.swing.*;
import java.awt.Font;
import javax.sound.sampled.*;
import javax.swing.border.*;
import java.util.Random;
import java.awt.event.*;
import java.awt.geom.Ellipse2D;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
public class RetroBall extends JPanel implements ActionListener
{  
    //Objects
    JFrame frame;
    Random random=new Random();
    Ellipse2D ball;
    Ellipse2D centerBall;
    //Image Objects
    Toolkit t=Toolkit.getDefaultToolkit();  
    Image mainImage=t.getImage("main.png");
    Image helpImage=t.getImage("help.png");
    Image ballColourChoice=t.getImage("ballcolour.png");

    //Button Objects
    JButton start=new JButton(new ImageIcon("startbtn.png"));
    JButton help=new JButton(new ImageIcon("helpbtn.png"));
    JButton back=new JButton(new ImageIcon("backbtn.png"));
    JButton white = new JButton(new ImageIcon("whitebtn.png"));
    JButton black = new JButton(new ImageIcon("blackbtn.png"));
    String filename="highscore.txt";
    File filescore=new File(filename);

    //Arrays
    int ORIGINAL_HEIGHT[]={40,80,120,160,200,240,280,320,360};
    int HEIGHT[]={40,80,120,160,200,240,280,320,360};
    int PILLAR[]={550,850,1150,1450};
    int PILLAR_RECUR[]={3,0,1,2};
    int PAGE[]={0,0,0,0,0};
    Color RGB[]={new Color(255,255,0), new Color(0,255,0), new Color(0,255,255), new Color(240,248,255), new Color(127,255,212) ,new  Color(255,140,0)};
    Color ballPillarColour[]={new Color(224,224,224), new Color(0,0,0)};
    //String Variables
    String s;
    String text;

    //Integer Variables
    int HIGH_SCORE=0;
    int CHANGE_THEME=5;
    int U;
    int X=50;
    int Y=200;
    int SCORE=0;
    int SLEEPTIME=10;
    int JP=0;
    int TMP=0;
    int JUMP=0;
    int COUNT=0;
    int VERTICAL_GAP=161;
    int HORIZONTAL_GAP=300;
    int M=5;
    int BALLSIZE=42;
    int N=0;
    int BUTTON_SOUND;
    int FALL=0;
    int R,G,B; 
    int CLASH=0;
    int MOVE_BACKGROUND=2;
    int INTRO_SOUND;
    boolean GAME_OVER=false;
    boolean END=false;
    boolean CLICKED;

    Color c=new Color(R,G,B);

    public static void main(String[] args)
    {
        RetroBall x=new RetroBall(0,false,1);
    }

    public void soundEffects()
    {
        try
        {
            AudioInputStream fly = AudioSystem.getAudioInputStream(new File("tap.wav").getAbsoluteFile());
            AudioInputStream clash = AudioSystem.getAudioInputStream(new File("out.wav").getAbsoluteFile());
            AudioInputStream buttonClick = AudioSystem.getAudioInputStream(new File("buttonClick.wav").getAbsoluteFile());
            AudioInputStream intro = AudioSystem.getAudioInputStream(new File("intro.wav").getAbsoluteFile());

            Clip clip = AudioSystem.getClip();

            if(CLASH==1)
            {
                clip.open(clash);
                clip.start();
                CLICKED=false;
            }
            if(CLICKED)
            {
                clip.open(fly);
                clip.start();
                CLICKED=false;
            } 
            if(INTRO_SOUND==1)
            {
                clip.open(intro);
                clip.start();
                INTRO_SOUND=0;
            }
            if(BUTTON_SOUND==1)
            {
                clip.open(buttonClick);
                clip.start();
                BUTTON_SOUND=0;
            }
        } catch (UnsupportedAudioFileException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } catch (LineUnavailableException e) {
            e.printStackTrace();
        }
    }

    public void actionPerformed(ActionEvent e)
    {     
        int gamestart=0;

        if(e.getSource() == white)
        {
            CHANGE_THEME=1;
            gamestart=1;
            BUTTON_SOUND=1;
            soundEffects();
        }
        if(e.getSource() ==black)
        {
            CHANGE_THEME=0;
            gamestart=1;
            BUTTON_SOUND=1;
            soundEffects();
        }
        if(gamestart==1)
        {
            playButtons();
        }
        if(e.getSource() == start)
        {
            startButton();
            BUTTON_SOUND=1;
            soundEffects();
        }
        if(e.getSource() == help)
        {
            helpButton();
            BUTTON_SOUND=1;
            soundEffects();
        }
        if(e.getSource() == back)
        {
            backButton();
            BUTTON_SOUND=1;
            soundEffects();
        }
    }

    public void startButton()
    {
        PAGE[1]=1;
        PAGE[2]=0;
        PAGE[3]=1;
        PAGE[0]=0;

        white.setVisible(true);
        black.setVisible(true);
        start.setVisible(false);
        help.setVisible(false);
        back.setVisible(true);
    }

    public void playButtons()
    {
        JP=1;
        PAGE[4]=1;
        PAGE[1]=0;
        PAGE[2]=0;
        PAGE[3]=0;
        PAGE[0]=0;
        CLICKED=true;
        JUMP=1;

        white.setVisible(false);
        black.setVisible(false);
        start.setVisible(false);
        help.setVisible(false);
        back.setVisible(false);
    }

    public void helpButton()
    {
        PAGE[2]=1;
        PAGE[3]=1;
        PAGE[0]=0;
        PAGE[1]=0;

        white.setVisible(false);
        black.setVisible(false);
        start.setVisible(false);
        help.setVisible(false);
        back.setVisible(true);
    }

    public void backButton()
    {
        PAGE[0]=1;
        PAGE[3]=1;
        PAGE[4]=0;
        PAGE[1]=0;
        PAGE[2]=0;

        white.setVisible(false);
        black.setVisible(false);
        start.setVisible(true);
        help.setVisible(true);
        back.setVisible(false);
    }

    public RetroBall(int c, boolean starttrue, int intro)
    {
        frame = new JFrame("Retro Ball");
        BorderLayout blayout = new BorderLayout();
        setFocusable(true);
        requestFocusInWindow();
        PAGE[0]=1;
        CHANGE_THEME=c;
        INTRO_SOUND=intro;
        soundEffects();
        initialShuffleHeight(HEIGHT);

        if(starttrue)
        {
            startButton();
        }
        try
        {
            FileReader fr=new FileReader(filename);
            BufferedReader br=new BufferedReader(fr);

            text=br.readLine();
            HIGH_SCORE=Integer.parseInt(text);
        }
        catch(IOException e)
        {
            System.err.println(e);
        }
        frame.setLayout(blayout);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(null);
        addMouseListener(new MouseListener(){
                public void mousePressed(MouseEvent e) {
                    soundEffects();
                    if(JP==1) 
                    {   
                        CLICKED=true;
                        white.setVisible(false);
                        JUMP=1;
                        M=12;
                        FALL=0;
                        N=0;
                        COUNT=0;
                    }
                } 

                public void mouseClicked(MouseEvent e) {}  

                public void mouseEntered(MouseEvent e) {}  

                public void mouseExited(MouseEvent e) {}  

                public void mouseReleased(MouseEvent e) {}});

        addKeyListener(new KeyListener() { 
                public void keyTyped(KeyEvent e)
                {
                }

                public void keyReleased(KeyEvent e) 
                {

                }

                public void keyPressed(KeyEvent e) 
                {   
                    int keyCode = e.getKeyCode();
                    if(keyCode==KeyEvent.VK_SPACE && JP==1)
                    {
                        soundEffects();
                        CLICKED=true;
                        white.setVisible(false);
                        JUMP=1;
                        M=12;
                        FALL=0;
                        N=0;
                        COUNT=0;
                    }
                }});

        this.add(start);
        this.add(help);

        start.addActionListener(this);
        start.setBounds(105,460,80,50);

        help.addActionListener(this);
        help.setBounds(245,460,80,50);

        back.addActionListener(this);
        back.setBounds(320,460,80,50);   

        white.addActionListener(this);
        white.setBounds(240,230,140,200); 

        black.addActionListener(this);
        black.setBounds(40,230,140,200); 

        setBackground(Color.gray);

        frame.setSize(430,700);
        frame.setResizable(false);
        frame.setLocationRelativeTo(null);
        frame.add(this);
        frame.setVisible(true);
    }

    public int multiColourScore()
    {
        int r=random.nextInt(3);

        return r;
    }

    public void scoreInformationPopUp()
    {
        try
        {
            JOptionPane.showMessageDialog(frame,"OUT!");
            FileWriter fw=new FileWriter(filename,true);
            BufferedWriter bw=new BufferedWriter(fw);
            PrintWriter pw= new PrintWriter(bw);
            if(SCORE>HIGH_SCORE)
            {    
                filescore.delete();
                FileWriter out=new FileWriter(filename);
                out.write(SCORE);
                pw.println(Integer.toString(SCORE));
                pw.close();   
                JOptionPane.showMessageDialog(frame, "NEW HIGHSCORE "+SCORE);
            }
            else if(SCORE<HIGH_SCORE)
            {
                JOptionPane.showMessageDialog(frame, "SCORE : "+SCORE+"         HIGHSCORE : "+text);
            }
        }
        catch(IOException e)
        {
            System.err.println(e);
        }
    }

    public void playAgain()
    {
        int n;
        if(!GAME_OVER)
        {
            GAME_OVER=true;
            scoreInformationPopUp();

            n=JOptionPane.showConfirmDialog(frame, "           PLAY AGAIN ?", "Question", JOptionPane.YES_NO_OPTION);
            if(n==JOptionPane.YES_OPTION)
            {
                frame.dispose();
                RetroBall retry=new RetroBall(CHANGE_THEME,true,0);
            }
            else if(n==JOptionPane.NO_OPTION)
            {
                frame.dispose();
            }
        }
    }

    public void scoreCalculator()
    {
        for(int i=0;i<4;i++)
        {
            if((X==PILLAR[i]+52) && !collisionMechanism())
            {
                SCORE++;                
            }
        }
    }

    public boolean collisionMechanism()
    {
        int r=0;
        for(int i=0;i<4;i++)
        {
            if(ball.intersects(PILLAR[i],55,105,HEIGHT[i]))
            {
                r=1;
                break;
            }
            if(ball.intersects(PILLAR[i],55+HEIGHT[i]+VERTICAL_GAP,105,650-(55+VERTICAL_GAP+HEIGHT[i])))
            {
                r=1;
                break;
            }
            if(ball.intersects(0,0,2000,55))
            {
                r=1;
                break;
            }
            if(ball.intersects(0,700,2000,55))
            {
                r=1;
                break;
            }
        }

        if(r==1)
        {             
            CLASH=1;
            soundEffects();
            return true;
        } 
        return false;
    }

    public void jumpMechanism()
    {
        if(JUMP==1)
        {
            if(COUNT%2==0)
            {
                Y-=M;
                M--;
            }
            COUNT++;
            if(COUNT==21)
            {
                JUMP=0;
                FALL=1;
            }
        }
        else if(FALL==1)
        {
            if(U%2==0)
            {
                Y+=N;
                N++;
            }
            U++;
        }
    }

    public void initialShuffleHeight(int[] array)
    {
        int index;
        for (int i=array.length-1;i>0;i--)
        {
            index = random.nextInt(i + 1);
            if (index != i)
            {
                array[index]=array[i];
                array[i]=array[index];
                array[index]=array[i];
            }
        }
    }

    public void paintComponent(Graphics g)
    { 
        super.paintComponent(g);
        Graphics2D g2 = (Graphics2D) g;
        setFont(new Font("November", Font.PLAIN,57));

        g2.setColor(RGB[CHANGE_THEME]);
        ball = new Ellipse2D.Double(X, Y, BALLSIZE, BALLSIZE);
        centerBall = new Ellipse2D.Double(X+8, Y+8, BALLSIZE-16, BALLSIZE-16);

        if(CHANGE_THEME==1)
        {
            g2.setColor(ballPillarColour[0]);
            g2.fill(ball);

            g2.setColor(ballPillarColour[1]);
            g2.fill(centerBall);
        }
        else
        {
            g2.setColor(ballPillarColour[1]);
            g2.fill(ball);

            g2.setColor(ballPillarColour[0]);
            g2.fill(centerBall);
        }

        if(PAGE[0]==1)
        {
            PAGE[3]=0;
            PAGE[1]=0;
            PAGE[2]=0;
            g.drawImage(mainImage, -5,100,this);  
        }
        if(PAGE[3]==1)
        {
            PAGE[0]=1;
            g.drawImage(mainImage, -5,100,this);  
            this.add(back);
        }   
        if(PAGE[4]==1)
        {
            PAGE[0]=0;
            PAGE[3]=0;
            PAGE[2]=0;
        }
        if(PAGE[2]==1)
        {
            PAGE[0]=0;
            g.drawImage(helpImage, -5,100,this);  
        }    
        if(PAGE[1]==1)
        {
            PAGE[0]=0;
            g.drawImage(ballColourChoice, -5,100,this); 
            this.add(white);
            this.add(black);
        }
        if(JP==1)
        {
            g.setColor(Color.black);
            g.fillRect(0,0,2000,55);

            g.setColor(ballPillarColour[0]);
            if(SCORE>=HIGH_SCORE)
            {
                g.setColor(RGB[multiColourScore()]);
            }
            g.drawString(Integer.toString(SCORE),195,53);
            for(int i=0;i<4;i++)
            {
                if(PILLAR[i]==-104)
                {
                    PILLAR[i]=PILLAR[PILLAR_RECUR[i]]+HORIZONTAL_GAP;
                    TMP=random.nextInt(HEIGHT.length);

                    HEIGHT[i]=ORIGINAL_HEIGHT[TMP];
                } 

                if(CHANGE_THEME==1)
                {
                    g.setColor(ballPillarColour[0]);
                    g.fillRect(PILLAR[i],55,105,HEIGHT[i]);
                    g.fillRect(PILLAR[i],55+HEIGHT[i]+VERTICAL_GAP,104,700-(55+VERTICAL_GAP+HEIGHT[i]));
                    g.setColor(ballPillarColour[1]);
                }
                else
                {
                    g.setColor(ballPillarColour[1]);
                    g.fillRect(PILLAR[i],55,105,HEIGHT[i]);
                    g.fillRect(PILLAR[i],55+HEIGHT[i]+VERTICAL_GAP,104,700-(55+VERTICAL_GAP+HEIGHT[i]));
                    g.setColor(ballPillarColour[0]);
                }
                g.fillRect(PILLAR[i]+20,55,65,HEIGHT[i]-20);
                g.fillRect(PILLAR[i]+20,(55+HEIGHT[i]+VERTICAL_GAP)+20,64,(700-(55+VERTICAL_GAP+HEIGHT[i])-40));
                PILLAR[i]-=MOVE_BACKGROUND;
            }
            if(CLASH==0)
            {
                scoreCalculator();  
                if(collisionMechanism())
                {
                    playAgain();
                } 
            }
        }
        jumpMechanism();
        try{
            Thread.sleep(SLEEPTIME);
        }
        catch(InterruptedException ie)
        {}
    }
}
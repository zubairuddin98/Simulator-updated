# Simulator-updated
RAM Scheduling Simulator
package simulator;
import java.util.Scanner;
import java.io.File;
import java.io.IOException;
import static java.lang.System.exit;
import java.sql.Array;
import javax.swing.JOptionPane;
import java.math.*;

public class Simulator {
    public
    static int[] ram;
    static int[] table;
    static int size;
    int fullf=0;
     static int emptyf;
    Simulator(){
        
    }
    void newprocess(int pid,int frames){
        int count=1;
        for(int i=0;i<size;i++)
        {
            if(ram[i]==-1&&count<=frames)
            {
                ram[i]=pid;
                count++;
            }
        }
        fullf+=frames;
        emptyf-=frames;
    }
    int phyaddr(int logaddr,int pgsize,int pid ){
        int frameno=logaddr/pgsize;
        int offset=logaddr%pgsize;
        int count=0;
        int i;
        for(i=0;i<size;i++)
        {
            if(ram[i]==pid)
            {
                if(count==frameno)
                {
                    break;
                }
                count++;                    
            }                
        }
       int phyaddr=((i)*pgsize)+offset;
        return phyaddr;
    }
    void printtable(int pid){
        int temp=0;
        System.out.println("PTPage Table for PID "+pid);
        System.out.println("PAGE  FRAME");
        for(int i=0;i<size;i++)
        {
            table[i]=-1;
            if(ram[i]==pid)
                temp++;
        }
        int temp2=0;
        for(int i=0;i<size;i++){
            if(i<temp)
            table[i]=temp2++;
            System.out.println(i+"  "+table[i]);
        }
    }
    void processref(){
        
    }
    int endprocess(int pid){
        int temp=0;
        for(int i=0;i<size;i++)
        {
            if(ram[i]==pid)
            {
                temp=1;
                ram[i]=-1;
                fullf--;
                emptyf++;
            }
            
        }
        if(temp==0)
            System.out.println("Wrong End process PID");   
        return emptyf;
    }
    void printmemory()
    {
        System.out.println("PM OS Frame Allocation");
        System.out.println("FRAME PID");
        for(int i=0;i<size;i++)
        {
            System.out.println(i+"   "+ram[i]);
        }
    }

    public static void main(String[] args) throws IOException {
        
        String file=new String();
        file=JOptionPane.showInputDialog(null,"Give address of text file");
            Scanner sc = new Scanner(new File(file));
     String s = sc.next();
     boolean check;
     int pgsize=0;
     int ramsize=0;
     int x;
     int pid,pmemory;
     Simulator sim=new Simulator();
    do{
           do {   
      switch(s){
          case "RAM":
              int temp=Integer.parseInt(sc.next());
              if(temp%2==0)
              ramsize=temp;
              else{
                  System.out.println("INVALID RAM SIZE");
                  exit(-1);
                  }
              s = sc.next();
              break;
          case "PAGESIZE":
              x=Integer.parseInt(sc.next());
              size=ramsize/x;
              ram=new int[size];
              table=new int[size];
              for(int i=0;i<size;i++)
                  ram[i]=-1;
              emptyf=size;
              //System.out.println("RAM "+size);
              //System.out.println("PAGESIZE "+x);
              if(x>0&&x<size)
              {
                  pgsize=x*1024*1024;
                  s = sc.next();
              }
              else
              {
                  System.out.println("ERROR in page size");
                  exit(-1);
              }
              
              break;
          case "NEW":
              int a;
                       
              pid=Integer.parseInt(sc.next());
              pmemory=Integer.parseInt(s=sc.next());
              float f=1024*1024;
              int frames=(int) Math.ceil(pmemory/f);
              System.out.println("NEW "+pid+" "+pmemory);
              if(frames<=emptyf)
              {
              sim.newprocess(pid, frames);
              System.out.println("ALLOCATED "+frames+" FRAMES,"+emptyf+" FRAMES STILL FREE");
              s=sc.next();
              int loop=1;
              if(s.equals("REF"))
              while(loop==1)
              {
                  int ref=Integer.parseInt(sc.next());
                  int logaddr=Integer.parseInt(sc.next());
                  if((logaddr<=pgsize*size)&&logaddr>=0){
                  System.out.println("REF "+ref+" LogAddr "+logaddr+" PhyAddr "+(sim.phyaddr(logaddr, pgsize, pid)));
                  s=sc.next();
                  if(!s.equals("REF"))
                      loop=0;
                  else
                      loop=1;
                  }
                  else{
                      System.out.println("REF "+ref+" LogAddr "+logaddr+" ILLEGAL ADDRESS");
                      s=sc.next();
                      loop=0;
                  }
              }
              }
              else{
                  System.out.println("FAILED: NOT ENOUGH FREE MEMORY");
                  s=sc.next();
              }
              break;
          case "END":
              pid=Integer.parseInt(sc.next());
              System.out.println("END "+pid+", NOW "+sim.endprocess(pid)+" FRAMES FREE");
              s = sc.next();
              break;
          case "PM":
              sim.printmemory();
              s = sc.next();
              break;
          case "PT":
              pid=Integer.parseInt(sc.next());
              sim.printtable(pid);
              s = sc.next();
              break;
          case "REF":
              int loop=1;
               while(loop==1)
               {
                  int ref=Integer.parseInt(sc.next());
                  int logaddr=Integer.parseInt(sc.next());
                  if(logaddr<size*pgsize&&logaddr>=0){
                  System.out.println("REF "+ref+" LogAddr "+logaddr+" PhyAddr "+sim.phyaddr(logaddr,pgsize,ref));
                  s=sc.next();
                  if(!s.equals("REF"))
                      loop=0;
                  else
                      loop=1;
                  }
                  else
                  {
                      System.out.println("REF "+ref+" LogAddr "+logaddr+" ILLEGAL ADDRESS");
                      s=sc.next();
                      loop=0;
                  }    
               }
               break;
          default:
              
              System.out.println(s);
              s = sc.next();
              break;
      }
    }while (sc.hasNext());
   System.out.println(s);
    sc.close();
    Scanner out=new Scanner(System.in);
    System.out.println("Press T to process again and F to exit");
  check=out.nextBoolean();
    }while(check);
  }
    }
    

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
    static int total_ram_frames;
    int full_frames=0;
     static int empty_frames;
    Simulator(){
        
    }
    /**
     * @param pid
     * @param frames 
     * @return  changes ram,full_frames,empty_frames
     */
    void new_process(int pid,int frames){
        int count=1;
        for(int i=0;i<total_ram_frames;i++)
        {
            if(ram[i]==-1&&count<=frames)
            {
                ram[i]=pid;
                count++;
            }
        }
        full_frames+=frames;
        empty_frames-=frames;
    }
    /**
     * 
     * @param logical_addr
     * @param page_size_bytes
     * @param pid
     * @return phy_addr
     */
    int phy_addr(int logical_addr,int page_size_bytes,int pid ){
        int frame_num=logical_addr/page_size_bytes;
        int offset=logical_addr%page_size_bytes;
        int count=0;
        int i;
        for(i=0;i<total_ram_frames;i++)
        {
            if(ram[i]==pid)
            {
                if(count==frame_num)
                {
                    break;
                }
                count++;                    
            }                
        }
       int phy_addr=((i)*page_size_bytes)+offset;
        return phy_addr;
    }
    /**
     * @param pid 
     * @return Prints respective process table
     */
    void print_table(int pid){
        int temp=0;
        System.out.println("PTPage Table for PID "+pid);
        System.out.println("PAGE  FRAME");
        for(int i=0;i<total_ram_frames;i++)
        {
            table[i]=-1;
            if(ram[i]==pid)
                temp++;
        }
        int temp2=0;
        for(int i=0;i<total_ram_frames;i++){
            if(i<temp)
            table[i]=temp2++;
            System.out.println(i+"  "+table[i]);
        }
    }
    void process_ref(){
        
    }
    /**
     * @param pid
     * @return changes RAM,full_frames and empty_frames
     */
    int end_process(int pid){
        int temp=0;
        for(int i=0;i<total_ram_frames;i++)
        {
            if(ram[i]==pid)
            {
                temp=1;
                ram[i]=-1;
                full_frames--;
                empty_frames++;
            }
            
        }
        if(temp==0)
            System.out.println("Wrong End process PID");   
        return empty_frames;
    }
    /**
     * @return Display current memory state
     */
    void print_memory()
    {
        System.out.println("PM OS Frame Allocation");
        System.out.println("FRAME PID");
        for(int i=0;i<total_ram_frames;i++)
        {
            System.out.println(i+"   "+ram[i]);
        }
    }

    public static void main(String[] args) throws IOException {
        
        String file=new String();
        file=JOptionPane.showInputDialog(null,"Give address of text file");
            Scanner sc = new Scanner(new File(file)); //taking address of file as input from user
     String s = sc.next();
     boolean check; //handeling last loop of termination of process
     int page_size_Bytes=0;
     int ramsize=0;
     int pid,process_memory;
     int page_size_MB=0;
     Simulator sim=new Simulator();
    do{
           do {   
      switch(s){
          case "RAM":
              int temp=Integer.parseInt(sc.next()); // taking RAM size
              if(temp%2==0)
              ramsize=temp; //setting RAM
              else{
                  System.out.println("INVALID RAM SIZE");
                  exit(-1);
                  }
              s = sc.next();    //Move to First word in the given string
              break;
          case "PAGESIZE":
              
              page_size_MB=Integer.parseInt(sc.next()); //getting page size
              
              //System.out.println("RAM "+size);
              //System.out.println("PAGESIZE "+x);
              if(page_size_MB>0&&page_size_MB<ramsize)  //check allowed pagesize given
              {
                  total_ram_frames=ramsize/page_size_MB;
                  ram=new int[total_ram_frames];    //RAM array declaration
                  table=new int[total_ram_frames];  //Table array declaration
                  for(int i=0;i<total_ram_frames;i++)
                       ram[i]=-1;
                  empty_frames=total_ram_frames;
                  page_size_Bytes=page_size_MB*1024*1024;   //page size conversion from MB to bytes
                  s = sc.next();    //move to next word in the given string
              }
              else
              {
                  System.out.println("ERROR in page size");
                  exit(-1);
              }
              
              break;
          case "NEW":
                           
              pid=Integer.parseInt(sc.next());  //new process ID
              process_memory=Integer.parseInt(s=sc.next()); //new process memory
              float frame_size_bytes=page_size_MB*1024*1024;    //Conversion of frame size from MB to Bytes
              int frames=(int) Math.ceil(process_memory/frame_size_bytes);  //frames required by new process
              System.out.println("NEW "+pid+" "+process_memory);
              if(frames<=empty_frames)
              {
              sim.new_process(pid, frames);
              System.out.println("ALLOCATED "+frames+" FRAMES,"+empty_frames+" FRAMES STILL FREE");
              s=sc.next();  //move to next command in the string
              int loop=1;   //used as flag to control the loop
              if(s.equals("REF"))
              while(loop==1)
              {
                  int ref=Integer.parseInt(sc.next());
                  int logical_addr=Integer.parseInt(sc.next());  //getting logical address given
                  if((logical_addr<=page_size_Bytes*total_ram_frames)&&logical_addr>=0){
                  System.out.println("REF "+ref+" LogAddr "+logical_addr+" PhyAddr "+(sim.phy_addr(logical_addr, page_size_Bytes, pid)));
                  s=sc.next();
                  if(!s.equals("REF"))
                      loop=0;
                  else
                      loop=1;
                  }
                  else{
                      System.out.println("REF "+ref+" LogAddr "+logical_addr+" ILLEGAL ADDRESS");
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
              System.out.println("END "+pid+", NOW "+sim.end_process(pid)+" FRAMES FREE");
              s = sc.next();
              break;
          case "PM":
              sim.print_memory();
              s = sc.next();
              break;
          case "PT":
              pid=Integer.parseInt(sc.next());
              sim.print_table(pid);
              s = sc.next();
              break;
          case "REF":
              int loop=1;
               while(loop==1)
               {
                  int ref=Integer.parseInt(sc.next());
                  int logical_addr=Integer.parseInt(sc.next());
                  if(logical_addr<total_ram_frames*page_size_Bytes&&logical_addr>=0){
                  System.out.println("REF "+ref+" LogAddr "+logical_addr+" PhyAddr "+sim.phy_addr(logical_addr,page_size_Bytes,ref));
                  s=sc.next();
                  if(!s.equals("REF"))
                      loop=0;
                  else
                      loop=1;
                  }
                  else
                  {
                      System.out.println("REF "+ref+" LogAddr "+logical_addr+" ILLEGAL ADDRESS");
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
  check=out.nextBoolean();  //taking user input
    }while(check);
  }
    }
    


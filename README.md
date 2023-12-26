# code54
import java.io.BufferedReader;
import java.io.DataOutputStream;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileReader;
import java.net.Socket;

public class Sentor {
    
    private String ipAddress = null;
    private String port = null;
    private Socket socket = null;

    public Sentor(String ipAddress,String port){
        this.ipAddress = ipAddress;
        this.port = port;
    }

    public boolean connect(){
        try{
            if(this.socket!=null){
                this.socket.close();
                this.socket = null;
            }
            Socket socket = new Socket(this.ipAddress,Integer.valueOf(this.port));
            this.socket = socket;
            return true;
        }catch(Exception e){
            return false;
        }
    }

    public boolean Sent(String filePath){
        File file = null;
        try{
            file = new File(filePath);
            BufferedReader br = new BufferedReader(new FileReader(file));
            br.readLine();
            br.close();
        }catch(Exception e){
            return false;
        }

        if(this.socket==null){
            if(!this.connect()){
                return false;
            }
        }

        try{
            DataOutputStream dos = new DataOutputStream(this.socket.getOutputStream());
            dos.writeUTF(file.getName());
            dos.flush();
            dos.writeLong(file.length());
            dos.flush();

            byte[] bytes = new byte[1024];
            int length = 0;

            FileInputStream fis = new FileInputStream(file);

            while((length = fis.read(bytes,0,bytes.length))!=-1){
                dos.write(bytes,0,length);
            }

            fis.close();
            this.socket.close();
            this.socket=null;
            return true;
        }catch(Exception e){

        }
        return false;
    }

    public static void main(String[] args){
        Sentor sentor = new Sentor("localhost", "8080");
        System.out.println(sentor.connect());
    }
}

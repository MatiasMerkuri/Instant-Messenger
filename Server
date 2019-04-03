import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.io.EOFException;
import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.net.ServerSocket;
import java.net.Socket;

public class Server extends JFrame {

    private JTextField userMsg;
    private JTextArea msgWindow;
    private ObjectOutputStream output;
    private ObjectInputStream input;
    private ServerSocket server;
    private Socket connection;

    public Server(){
        super("Instant Messenger");
        userMsg = new JTextField();
        userMsg.setEditable(false);

        // Listens for ENTER and when clicked will replace the text on the text bar with blank, as it has been sent.
        userMsg.addActionListener(
                new ActionListener() {
                    @Override
                    public void actionPerformed(ActionEvent e) {
                        sendMsg(e.getActionCommand());
                        userMsg.setText("");
                    }
                }
        );

        add(userMsg, BorderLayout.NORTH);
        msgWindow = new JTextArea();
        add(new JScrollPane(msgWindow));
        setSize(600, 450);
        setVisible(true);
        msgWindow.setEditable(false);

    }

    // Core method. Will be run in the main method, this will virtually begin the whole process
    public void startRunning(){
        try{
            server = new ServerSocket(3431, 100);
            while(true){
                try{
                    waitForConn();
                    setupStreams();
                    whileChatting();
                } catch (EOFException eofeException) {
                    showMsg("\nConnection Terminated");
                } finally {
                    close();
                }
            }
        } catch (IOException ioException){
           ioException.printStackTrace();
        }
    }

    // waitForConn method which will wait for a connection from a client
    private void waitForConn() throws IOException{
           showMsg("Waiting for connection...");
           connection = server.accept();
           showMsg("\nConnected to " + connection.getInetAddress().getHostName());
    }

    // setupStreams method which will setup the streams
    private void setupStreams() throws IOException{
        output = new ObjectOutputStream(connection.getOutputStream());
        output.flush();

        input = new ObjectInputStream(connection.getInputStream());
        showMsg("\nStreams established \n ");
    }

    // whileChatting method which will run whilst there is a connection unless the CLIENT TERMINATED the connection.
    // It will treat the object passed as a String
    private void whileChatting() throws IOException{
        String msg = " Connected to client ";
        sendMsg(msg);
        ableToType(true);

        do{
            try{
                msg = (String) input.readObject();
                showMsg("\n" + msg);
            } catch (ClassNotFoundException classNotFoundException) {
                showMsg("\n Error");

            }
        } while (!msg.equals("CLIENT - TERMINATE"));
    }

    // close method which will close all the sockets & connection
    private void close(){
        showMsg("\n Terminating connections... \n ");
        ableToType(false);
        try{
            output.close();
            input.close();
            connection.close();
        } catch (IOException ioException) {
            ioException.printStackTrace();
        }
    }

    // sendMsg method will be used for for the sending of the msgs from the server
    private void sendMsg(String msg){
        try{
            output.writeObject("SERVER - " + msg);
            output.flush();
            showMsg("\nSERVER - " + msg);
        } catch (IOException ioException) {
            msgWindow.append("\n Error: Could not send message");
        }
    }

    // showMessage method which basically sends a message in the msgWindow
    private void showMsg(final String txt){
        SwingUtilities.invokeLater(
                new Runnable() {
                    @Override
                    public void run() {
                        msgWindow.append(txt);
                    }
                }
        );
    }

    // ableToType method which will revoke or grant permission to type in the text bar based on the boolean passed
    private void ableToType(final boolean tOf){
        SwingUtilities.invokeLater(
                new Runnable() {
                    @Override
                    public void run() {
                        userMsg.setEditable(tOf);
                    }
                }
        );
    }

}

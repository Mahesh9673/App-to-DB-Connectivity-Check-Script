# App-to-DB-Connectivity-Check-Script




import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;
import java.io.Console;

public class DBConnectivityTest {
    public static void main(String[] args) throws Exception {
        if (args.length != 2) {
            System.out.println(
                "Usage: java DBConnectivityTest <jdbc-url> <username>");
            System.exit(2);
        }

        String url = args[0];
        String user = args[1];

        Console console = System.console();
        if (console == null) {
            System.err.println(
                "ERROR: No interactive console. Run directly in a terminal.");
            System.exit(2);
        }

        char[] passwordChars =
            console.readPassword("DB password: ");
        String password = new String(passwordChars);
        java.util.Arrays.fill(passwordChars, '\0');

        try (Connection conn =
                 DriverManager.getConnection(url, user, password);
             Statement stmt = conn.createStatement();
             ResultSet rs = stmt.executeQuery(
                 "SELECT 'SUCCESS' AS STATUS FROM dual")) {

            if (rs.next()) {
                System.out.println("DB CONNECTION SUCCESS");
                System.out.println("Query result: " + rs.getString("STATUS"));
            }
        } catch (Exception e) {
            System.err.println("DB CONNECTION FAILED");
            System.err.println(e.getClass().getSimpleName()
                + ": " + e.getMessage());
            System.exit(1);
        } finally {
            password = null;
        }
    }
}

==============================================================================


Save file & compile it:



/u01/jdk/bin/javac -cp /path/to/ojdbc11.jar DBConnectivityTest.java

Run it:--

/u01/jdk/bin/java -cp ".:/path/to/ojdbc11.jar" DBConnectivityTest 'jdbc:oracle:thin:@//10.10.10.20:1521/ORCLPDB1' APP_USER


  Enter the database password at the prompt. It won't be included in the command-line arguments.

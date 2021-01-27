# JDBC操作Oracle中的表格数据

- 增、删、改、查、传参

  ```java
  package connectDatabaseTest; 
  
  import java.sql.Connection;
  import java.sql.Driver;
  import java.sql.DriverManager;
  import java.sql.PreparedStatement;
  import java.sql.ResultSet;
  import java.sql.SQLException;
  
  public class connectDatabaseTest2 {
  	public static final String URL = "*";
  	public static final String USER = "*";
  	public static final String PASSWORD = "*";
  	
  	public static void main(String[] args) {
  		//Statement stmt = null;
  		PreparedStatement ps = null;
  		Connection conn = null;
  		ResultSet rs = null;
  		
  		
  		try {
  			// 加载驱动程序
  			Driver driver = new oracle.jdbc.driver.OracleDriver();
  			DriverManager.registerDriver(driver);
  			System.out.println("已加载驱动：" + driver);
  
  			// 连接数据库
  			conn = DriverManager.getConnection(URL, USER, PASSWORD);
  			System.out.println("已连接至：" + conn);
  
  			// 获取数据库操作对象
  			//stmt = conn.createStatement();
  			//System.out.println("已获取操作对象：" + stmt);
  
  			// 执行sql
  			//String sql = "SELECT * FROM CX_STAFF WHERE DEPARTMENT_ID = (SELECT DEPARTMENT_ID FROM CX_DEPARTMENT WHERE NAME = ?)";
  			//查询功能
  			ps = conn.prepareStatement("SELECT * FROM CX_STAFF CT WHERE CT.DEPARTMENT_ID = (SELECT DEPARTMENT_ID FROM CX_DEPARTMENT WHERE NAME = ?)");
  			ps.setString(1, "李华");
  			
  			//WHERE DEPARTMENT_ID = (SELECT DEPARTMENT_ID FROM CX_DEPARTMENT WHERE NAME = '李明')
  			// 增：INSERT INTO CX_STAFF VALUES(0005,'吴为',01,TO_DATE('2021-01-01','YYYY-MM-DD'))
  			// 删：DELETE FROM CX_STAFF WHERE NAME = '吴为'
  			// 改：UPDATE CX_STAFF SET DEPARTMENT_ID = 02 WHERE ID = 0005
  			// 查：SELECT DEPARTMENT_ID,ID,NAME,HIRE_DATE FROM CX_STAFF WHERE DEPARTMENT_ID =
  			// (SELECT DEPARTMENT_ID FROM CX_DEPARTMENT WHERE NAME = '李明'）
  			/*
  			 * 增删改： int count = stmt.executeUpdate(sql); System.out.println(count == 1 ?
  			 * "数据更新成功" : "数据更新失败");
  			 */
  			rs = ps.executeQuery();
  			//System.out.println(rs.next());
  			 while(rs.next()){
  				 //System.out.println(rs.getInt("ID"));
  				 System.out.println("部门编号："+rs.getInt("DEPARTMENT_ID")+" 员工编号："+rs.getInt("ID")+" 姓名："+rs.getString("NAME")+" 入职时间：" + rs.getDate("HIRE_DATE"));
  			 }
  			 
  		} catch (SQLException e) {
  			e.printStackTrace();
  		} finally {
  			// 释放资源
  			if (ps != null) {
  				try {
  					ps.close();
  				} catch (SQLException e) {
  					e.printStackTrace();
  				}
  			}
  			if (conn != null) {
  				try {
  					conn.close();
  				} catch (SQLException e) {
  					e.printStackTrace();
  				}
  			}
  			if (rs != null) {
  				try {
  					rs.close();
  				} catch (SQLException e) {
  					e.printStackTrace();
  				}
  			}
  
  		}
  	}
  }
  
  ```

 


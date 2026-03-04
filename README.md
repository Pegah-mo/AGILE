using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
//Import namespace OleDb for databases (outside class)
using System.Data.OleDb;
//System.Data for command object
using System.Data;
namespace Tietokantaa
{

    class Customer
    {
        private int custID;
        private string name;
        private string area;
        private double balance;

        public string Name
        {
            get { return name; }
        }

        public string Area
        {
            get { return area; }
        }

        public int CustID
        {
            get { return custID; }
        }

        public double Balance
        {
            get { return balance; }
        }

        public Customer(int c, string nm, string ar, double bal)
        {

            custID = c;
            name = nm;
            area = ar;
            balance = bal;
        }

        public override string ToString()
        {
            return name;
        }
    }

    class DataService
    {
        private OleDbConnection myConnection;

        public DataService()
        {
            //In class method(s), create and open connection
            //This can be done either once (e.g. Page_Load) for each
            //page request, or separately every time db connection is required
            String connstr;
            //set the path here acording to the location of database folder
            String projectPath = @"..\..\..\Data";
            connstr = "Provider = Microsoft.ACE.OLEDB.12.0;" + @"Data Source = " +
            projectPath + @"\CustomerOrders2019.accdb;";
            //OleDbConnection requires namespace System.Data.OleDb
            myConnection = new OleDbConnection();
            myConnection.ConnectionString = connstr;
            myConnection.Open();
        }

        private OleDbDataReader GetData(string[] fields, string table)
        {
            OleDbCommand myCommand = new OleDbCommand();

            myCommand.Connection = myConnection;
            //SQL query string
            myCommand.CommandText = "SELECT ";

            foreach (string s in fields)
                myCommand.CommandText += s + ", ";

            myCommand.CommandText = myCommand.CommandText.Remove(myCommand.CommandText.LastIndexOf(","));
            myCommand.CommandText += " FROM " + table;
            //CommandType requires namespace System.Data
            myCommand.CommandType = CommandType.Text;

            //Execute the SQL request command and
            //store the output in myReader object
            OleDbDataReader myReader;
            myReader = myCommand.ExecuteReader();

            return myReader;
        }

        private OleDbDataReader GetDataWhereString(string[] fields, string table, string keyField, string keyValue)
        {
            OleDbCommand myCommand = new OleDbCommand();

            myCommand.Connection = myConnection;
            //SQL query string
            myCommand.CommandText = "SELECT ";

            foreach (string s in fields)
                myCommand.CommandText += s + ", ";

            myCommand.CommandText = myCommand.CommandText.Remove(myCommand.CommandText.LastIndexOf(","));
            myCommand.CommandText += " FROM " + table;

            myCommand.CommandText += " WHERE " + keyField + "='" + keyValue + "';";
            //CommandType requires namespace System.Data
            myCommand.CommandType = CommandType.Text;


            //CommandType requires namespace System.Data
            myCommand.CommandType = CommandType.Text;

            //Execute the SQL request command and
            //store the output in myReader object
            OleDbDataReader myReader;
            myReader = myCommand.ExecuteReader();

            return myReader;
        }

        private OleDbDataReader GetDataWhereBetween(string[] fields, string table, string keyField, double minValue, double maxValue)
        {
            OleDbCommand myCommand = new OleDbCommand();

            myCommand.Connection = myConnection;
            //SQL query string
            myCommand.CommandText = "SELECT ";

            foreach (string s in fields)
                myCommand.CommandText += s + ", ";

            myCommand.CommandText = myCommand.CommandText.Remove(myCommand.CommandText.LastIndexOf(","));
            myCommand.CommandText += " FROM " + table;

            myCommand.CommandText += " WHERE " + keyField + " BETWEEN " + minValue + " AND " + maxValue + ";";
            //CommandType requires namespace System.Data
            myCommand.CommandType = CommandType.Text;


            //CommandType requires namespace System.Data
            myCommand.CommandType = CommandType.Text;

            //Execute the SQL request command and
            //store the output in myReader object
            OleDbDataReader myReader;
            myReader = myCommand.ExecuteReader();

            return myReader;
        }


        public Customer GetCustomerByName(string custName)
        {
            Customer newCust = null;
            string[] fields = { "CustID", "Name", "Balance", "Area" };
            string table = "Customer";

            //Execute the SQL request command and
            //store the output in myReader object
            OleDbDataReader myReader;
            myReader = GetDataWhereString(fields, table, "Name", custName);
            //This method allows to control the reading of database response rows
            bool notEoF;
            //read first row from database
            notEoF = myReader.Read();
            //read row by row until the last row
            while (notEoF) //continue reading if not yet all read
            {
                int custID = Convert.ToInt32(myReader["CustId"].ToString());
                string name = myReader["Name"].ToString();
                string area = myReader["Area"].ToString(); ;
                double balance = Convert.ToDouble(myReader["Balance"].ToString()); ;
                newCust = new Customer(custID, name, area, balance);
                break;
            }
            return newCust;
        }

        public List<Customer> GetAllCustomersWhere(string field, double min, double max)
        {
            List<Customer> custList = new List<Customer>();

            OleDbCommand myCommand = new OleDbCommand();

            string[] fields = { "CustID", "Name", "Balance", "Area" };
            string table = "Customer";

            //Execute the SQL request command and
            //store the output in myReader object
            OleDbDataReader myReader;
            myReader = GetDataWhereBetween(fields, table, field, min, max);

            //This method allows to control the reading of database response rows
            bool notEoF;
            //read first row from database
            notEoF = myReader.Read();
            //read row by row until the last row
            while (notEoF) //continue reading if not yet all read
            {
                int custID = Convert.ToInt32(myReader["CustId"].ToString());
                string name = myReader["Name"].ToString();
                string area = myReader["Area"].ToString(); ;
                double balance = Convert.ToDouble(myReader["Balance"].ToString()); ;

                Customer newC = new Customer(custID, name, area, balance);

                custList.Add(newC);

                //output item on list box
                //                Console.Write(myReader["name"].ToString() + ": ");
                //                Console.WriteLine(myReader["CustId"].ToString());
                //read next row
                notEoF = myReader.Read();
            }

            return custList;
        }

        public List<Customer> GetAllCustomers()
        {
            List<Customer> custList = new List<Customer>();

            OleDbCommand myCommand = new OleDbCommand();

            string[] fields = { "CustID", "Name", "Balance", "Area" };
            string table = "Customer";

            //Execute the SQL request command and
            //store the output in myReader object
            OleDbDataReader myReader;
            myReader = GetData(fields, table);

            //This method allows to control the reading of database response rows
            bool notEoF;
            //read first row from database
            notEoF = myReader.Read();
            //read row by row until the last row
            while (notEoF) //continue reading if not yet all read
            {
                int custID = Convert.ToInt32(myReader["CustId"].ToString());
                string name = myReader["Name"].ToString();
                string area = myReader["Area"].ToString(); ;
                double balance = Convert.ToDouble(myReader["Balance"].ToString()); ;

                Customer newC = new Customer(custID, name, area, balance);

                custList.Add(newC);

                //output item on list box
                //                Console.Write(myReader["name"].ToString() + ": ");
                //                Console.WriteLine(myReader["CustId"].ToString());
                //read next row
                notEoF = myReader.Read();
            }

            return custList;
        }

    }

    class MyApplication
    {
        DataService myDataService;

        public MyApplication()
        {
            myDataService = new DataService();
        }

        public string GetAllCustomers()
        {
            string customers = "";
            foreach (Customer c in myDataService.GetAllCustomers())
                customers += c.ToString() + "\n";
            customers = customers.Remove(customers.LastIndexOf('\n'));
            return customers;
        }

        public string GetCustomersByBalance(double min, double max)
        {
            string customers = "";
            foreach (Customer c in myDataService.GetAllCustomersWhere("Balance", min, max))
                customers += c.ToString() + "\n";
            customers = customers.Remove(customers.LastIndexOf('\n'));
            return customers;
        }


        public Customer GetCustomerDataByName(string custName)
        {
            return myDataService.GetCustomerByName(custName);
        }

    }


    class UI
    {
        MyApplication myApp = new MyApplication();

        public void ShowMenu()
        {
            Console.WriteLine("In this app you can (select with number):");
            Console.WriteLine("1. show all customers");
            Console.WriteLine("2. show data of one customer only");
            Console.WriteLine("exit (to finish)");
        }

        private void ShowListEnumerated(string[] stringList)
        {
            for (int i = 0; i < stringList.Length; i++)
                Console.WriteLine((i + 1) + ": " + stringList[i]);
        }

        private void ShowOneCustomer()
        {
            bool goOn = true, success;
            int custNr;
            while(goOn)
            {
                Console.Clear();
                string[] customers = myApp.GetAllCustomers().Split('\n');
                Console.WriteLine("Enter the customer number you want to see:");

                if (customers == null || customers.Length == 0)
                {
                    Console.WriteLine("No customers available in the database");
                    break;
                }
                else
                {
                    success = false;
                    //Customer list
                    while (!success)
                    {
                        //Show customer list
                        ShowListEnumerated(customers);
                        Console.WriteLine("Enter customer number:");
                        try
                        {
                            custNr = Convert.ToInt32(Console.ReadLine());
                            if (custNr < 1 || custNr > customers.Length)
                            {
                                throw new Exception("Invalid customer number: You can only select from the listed customer numbers");
                            }

                            Customer cust = myApp.GetCustomerDataByName(customers[custNr - 1]);
                            if(cust == null)
                            {
                                throw new Exception("Invalid customer data");
                            }
                            Console.WriteLine(cust.Name + ": " + cust.CustID + ", " + cust.Area);
                            success = true;
                        }
                        catch
                        {
                            Console.WriteLine("Invalid customer number: You can only select from the listed customer numbers 1 - " + customers.Length);
                        }
                    }

                }

                Console.WriteLine("Want to see another customer data (Y/N)?");
                if (Console.ReadLine() != "Y")
                    goOn = false;
                Console.WriteLine();
            }
            
        }

        public void Run()
        {
            ShowMenu();
            string command = Console.ReadLine();

            while (true)
            {
                switch (command)
                {
                    case "1":
                        Console.Clear();
                        Console.Write(myApp.GetAllCustomers());
                        Console.WriteLine();
                        break;
                    case "2":
                        Console.Clear();
                        ShowOneCustomer();
                        break;
                    case "exit":
                        Console.WriteLine("Press any key to close the program");
                        Console.ReadLine();
                        return;
                        
                    default:
                        Console.WriteLine("Invalid input: You can only select from given options");
                        break;
                }
                ShowMenu();
                command = Console.ReadLine();
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            UI myUI = new UI();
            myUI.Run();
        }
    }
}

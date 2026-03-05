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

    public enum StoryState
    {
        ProjectBacklog,
        InSprint,
        Done
    }

    //Person classes
    public class Person
    {
        private int personId;
        private string name;
        private string role;
        private string email;

        public int PersonId
        {
            get { return personId; }
        }

        public string Name
        {
            get { return name; }
        }

        public string Role
        {
            get { return role; }
        }

        public string Email
        {
            get { return email; }
        }

        public Person(int pId, string nm, string rol, string ead)
        {

            personId = pId;
            name = nm;
            role = rol;
            email = ead;
        }
        public override string ToString()
        {
            return name;
        }
    }

    //user story class

        class UserStory
    {
        private int storyId;
        private int projectId;
        private string title;
        private string description;
        private int priority;
        private StoryState state;

        public int StoryId          { get { return storyId; } }
        public int ProjectId        { get { return projectId; } }
        public string Title         { get { return title; } }
        public string Description   { get { return description; } }
        public int Priority         { get { return priority; } }
        public StoryState State     { get { return state; } }

        public UserStory(int id, int projId, string ttl, string desc, int prio, StoryState st)
        {
            storyId     = id;
            projectId   = projId;
            title       = ttl;
            description = desc;
            priority    = prio;
            state       = st;
        }

        public override string ToString()
        {
            return title;
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

        //methodes=======================================================================================================================
        //userstory method========================

        public List<UserStory> GetStoriesByProject(int projectId)
        {
            List<UserStory> list = new List<UserStory>();

            string[] fields = { "storyId", "projectId", "title", "description", "priority", "state" };
            OleDbDataReader myReader = GetDataWhereInt(fields, "UserStory", "projectId", projectId);

            while (myReader.Read())
            {
                int id      = Convert.ToInt32(myReader["storyId"].ToString());
                int projId  = Convert.ToInt32(myReader["projectId"].ToString());
                string title = myReader["title"].ToString();
                string desc  = myReader["description"].ToString();
                int prio    = Convert.ToInt32(myReader["priority"].ToString());
                StoryState state = (StoryState)Convert.ToInt32(myReader["state"].ToString());

                list.Add(new UserStory(id, projId, title, desc, prio, state));
            }

            return list;
        }

        public void AddUserStory(int projectId, string title, string description, int priority)
        {
                    (int)StoryState.ProjectBacklog = 0
                    string sql = "INSERT INTO UserStory (projectId, title, description, priority, state) VALUES (" +
                    projectId + ", '" +
                    title + "', '" +
                    description + "', " +
                    priority + ", " +
                    (int)StoryState.ProjectBacklog + ");";

            ExecuteNonQuery(sql);
        }

        public void UpdateStoryState(int storyId, StoryState newState)
        {
                    string sql = "UPDATE UserStory SET state = " +
                    (int)newState +
                    " WHERE storyId = " + storyId + ";";

            ExecuteNonQuery(sql);
        }

        public void DeleteUserStory(int storyId)
        {
            string sqlTasks = "DELETE FROM Task WHERE storyId = " + storyId + ";";
            ExecuteNonQuery(sqlTasks);
            string sqlStory = "DELETE FROM UserStory WHERE storyId = " + storyId + ";";
            ExecuteNonQuery(sqlStory);
        }
       
        //Person classes
        public List<Person> GetAllPersons()
        {
            List<Person> personList = new List<Person>();
        
            string[] fields = { "PersonID", "PersonName", "PersonRole", "Email" };
            string table = "Person";
        
            OleDbDataReader myReader;
            myReader = GetData(fields, table);
        
            bool notEoF;
            notEoF = myReader.Read();
        
            while (notEoF)
            {
                int id = Convert.ToInt32(myReader["PersonID"].ToString());
                string name = myReader["PersonName"].ToString();
                string role = myReader["PersonRole"].ToString();
                string email = myReader["Email"].ToString();
        
                Person newP = new Person(id, name, role, email);
        
                personList.Add(newP);
        
                notEoF = myReader.Read();
            }
        
            return personList;
        }
        
        public Person GetPersonByName(string personName)
        {
            Person newPerson = null;
        
            string[] fields = { "PersonID", "PersonName", "PersonRole", "Email" };
            string table = "Person";
        
            OleDbDataReader myReader;
            myReader = GetDataWhereString(fields, table, "PersonName", personName);
        
            bool notEoF;
            notEoF = myReader.Read();
        
            while (notEoF)
            {
                int id = Convert.ToInt32(myReader["PersonID"].ToString());
                string name = myReader["PersonName"].ToString();
                string role = myReader["PersonRole"].ToString();
                string email = myReader["Email"].ToString();
        
                newPerson = new Person(id, name, role, email);
        
                break;
            }
        
            return newPerson;
        }
        
        public void AddPerson(int id, string name, string role, string email)
        {
            OleDbCommand myCommand = new OleDbCommand();
        
            myCommand.Connection = myConnection;
        
            myCommand.CommandText =
                "INSERT INTO Person(PersonID, PersonName, PersonRole, Email) VALUES (" +
                id + ", '" + name + "', '" + role + "', '" + email + "')";
        
            myCommand.CommandType = CommandType.Text;
        
            myCommand.ExecuteNonQuery();
        }
        
        public void RemovePerson(int id)
        {
            OleDbCommand myCommand = new OleDbCommand();
        
            myCommand.Connection = myConnection;
        
            myCommand.CommandText =
                "DELETE FROM Person WHERE PersonID = " + id;
        
            myCommand.CommandType = CommandType.Text;
        
            myCommand.ExecuteNonQuery();
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

        public List<Person> GetAllPersons()
        {
            return myDataService.GetAllPersons();
        }
        
        public Person GetPersonDataByName(string personName)
        {
            return myDataService.GetPersonByName(personName);
        }
        
        public void AddPerson(int id, string name, string role, string email)
        {
            myDataService.AddPerson(id, name, role, email);
        }
        
        public void RemovePersonById(int id)
        {
            myDataService.RemovePerson(id);
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

            //Person menu
            Console.WriteLine("3. show all persons");
            Console.WriteLine("4. show data of one person only (by name)");
            Console.WriteLine("5. add person");
            Console.WriteLine("6. remove person (by id)");
            
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

        //Person classes
        private void ShowAllPersons()
        {
            Console.Clear();
            List<Person> persons = myApp.GetAllPersons();

            if (persons.Count == 0)
            {
                Console.WriteLine("No persons in database.");
                return;
            }

            foreach (Person p in persons)
                Console.WriteLine($"{p.PersonId}: {p.Name} ({p.Role}) <{p.Email}>");
        }

        private void ShowOnePerson()
        {
            Console.Clear();
            Console.Write("Enter name: ");
            string name = Console.ReadLine();

            Person p = myApp.GetPersonDataByName(name);
            if (p == null)
            {
                Console.WriteLine("Person not found.");
                return;
            }

            Console.WriteLine($"{p.PersonId}: {p.Name} ({p.Role}) <{p.Email}>");
        }

        private void AddPerson()
        {
            Console.Clear();
            try
            {
                Console.Write("PersonID (int): ");
                int id = Convert.ToInt32(Console.ReadLine());

                Console.Write("Name: ");
                string name = Console.ReadLine();

                Console.Write("Role: ");
                string role = Console.ReadLine();

                Console.Write("Email: ");
                string email = Console.ReadLine();

                myApp.AddPerson(id, name, role, email);
                Console.WriteLine("Added.");
            }
            catch
            {
                Console.WriteLine("Failed to add.");
            }
        }

        private void RemovePerson()
        {
            Console.Clear();
            try
            {
                Console.Write("PersonID to remove: ");
                int id = Convert.ToInt32(Console.ReadLine());

                myApp.RemovePersonById(id);
                Console.WriteLine("Removed (if existed).");
            }
            catch
            {
                Console.WriteLine("Failed to remove.");
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
                        
                    //Person cases
                    case "3":
                        ShowAllPersons();
                        break;
    
                    case "4":
                        ShowOnePerson();
                        break;
    
                    case "5":
                        AddPerson();
                        break;
    
                    case "6":
                        RemovePerson();
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

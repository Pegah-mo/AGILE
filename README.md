using System;
using System.Collections.Generic;
//System.Data for command object
using System.Data;
//Import namespace OleDb for databases (outside class)
using System.Data.OleDb;
using System.Linq;
using System.Reflection.Emit;
using System.Text;
using System.Threading.Tasks;
using Tietokantaa;
namespace Tietokantaa
{

    public enum StoryState
    {
        ProjectBacklog = 0,
        InSprint = 1,
        Done = 2
    }
    public enum TaskState
    {
        ToBeDone = 0,
        InProcess = 1,
        Done = 2
    }

    //Project classes
    class Project
    {
        private int projectId;
        private string name;
        private string description;
        private DateTime startDate;
        private DateTime endDate;


        public int ProjectId { get { return projectId; } }
        public string Name { get { return name; } }
        public string Description { get { return description; } }
        public DateTime StartDate { get { return startDate; } }
        public DateTime EndDate { get { return endDate; } }

        public Project(int proId, string nm, string desc, DateTime start, DateTime end)
        {
            projectId = proId;
            name = nm;
            description = desc;
            startDate = start;
            endDate = end;
        }


        public override string ToString() { return name; }
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

    // Team Class
    class Team
    {
        private int teamId;
        private string name;


        public int TeamId { get { return teamId; } }
        public string Name { get { return name; } }

        public Team(int id, string nm)
        {
            teamId = id;
            name = nm;
        }
        public override string ToString() { return name; }


    }


    // Task class
    public class Task
    {
        private int taskId;
        private int storyId;
        private string title;
        private string description;
        private int priority;
        private TaskState state;
        private string labels;
        private string assignedPerson;

        public int TaskId { get { return taskId; } }
        public int StoryId { get { return storyId; } }
        public string Title { get { return title; } }
        public string Description { get { return description; } }
        public int Priority { get { return priority; } }
        public TaskState State { get { return state; } }
        public string Labels { get { return labels; } }
        public string AssignedPerson { get { return assignedPerson; } }

        public Task(int id, int stId, string ttl, string desc, int prio, TaskState st, string lbls, string person)
        {
            taskId = id;
            storyId = stId;
            title = ttl;
            description = desc;
            priority = prio;
            state = st;
            labels = lbls;
            assignedPerson = person;
        }

        public override string ToString()
        {
            return title;
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

        public int StoryId { get { return storyId; } }
        public int ProjectId { get { return projectId; } }
        public string Title { get { return title; } }
        public string Description { get { return description; } }
        public int Priority { get { return priority; } }
        public StoryState State { get { return state; } }

        public UserStory(int id, int projId, string ttl, string desc, int prio, StoryState st)
        {
            storyId = id;
            projectId = projId;
            title = ttl;
            description = desc;
            priority = prio;
            state = st;
        }

        public override string ToString()
        {
            return title;
        }
    }



//data serice class


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
            String projectPath = @"D:\Desktop\Third semester\agile"; //Pegah Laptop
            connstr = "Provider=Microsoft.ACE.OLEDB.12.0;" +
                             @"Data Source=" + projectPath + @"\AgileDB-version-01.accdb;";
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

        private OleDbDataReader GetDataWhereInt(string[] fields, string table,
                                              string keyField, int keyValue)
        {
            OleDbCommand myCommand = new OleDbCommand();

            myCommand.Connection = myConnection;
            //SQL query string
            myCommand.CommandText = "SELECT ";

            foreach (string s in fields)
                myCommand.CommandText += s + ", ";

            myCommand.CommandText = myCommand.CommandText.Remove(myCommand.CommandText.LastIndexOf(","));
            myCommand.CommandText += " FROM " + table;

            myCommand.CommandText += " WHERE " + keyField + " = " + keyValue + ";";
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


        private void ExecuteNonQuery(string sql) //for update, insert, delete
        {
            OleDbCommand myCommand = new OleDbCommand();
            myCommand.Connection  = myConnection;
            myCommand.CommandText = sql;
            myCommand.CommandType = CommandType.Text;
            myCommand.ExecuteNonQuery();
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
                int id = Convert.ToInt32(myReader["storyId"].ToString());
                int projId = Convert.ToInt32(myReader["projectId"].ToString());
                string title = myReader["title"].ToString();
                string desc = myReader["description"].ToString();
                int prio = Convert.ToInt32(myReader["priority"].ToString());
                StoryState state = (StoryState)Convert.ToInt32(myReader["state"].ToString());

                list.Add(new UserStory(id, projId, title, desc, prio, state));
            }

            return list;
        }    

        public void AddUserStory(int projectId, string title, string description, int priority)
        {
            
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

        //Project class

        public List<Project> GetAllProjects()
        {
            List<Project> projectList = new List<Project>();

            string[] fields = { "projectId", "name", "description", "startDate", "endDate" };
            string table = "Project";

            OleDbDataReader myReader;
            myReader = GetData(fields, table);

            bool condition;
            condition = myReader.Read();

            while (condition)
            {
                int id = Convert.ToInt32(myReader["projectId"].ToString());
                string nm = myReader["name"].ToString();
                string desc = myReader["description"].ToString();
                DateTime start = Convert.ToDateTime(myReader["startDate"].ToString());
                DateTime end = Convert.ToDateTime(myReader["endDate"].ToString());

                Project newP = new Project(id, nm, desc, start, end);
                projectList.Add(newP);

                condition = myReader.Read();
            }

            return projectList;
        }


        public Project GetProjectById(int projectId)
        {
            Project newProject = null;

            string[] fields = { "projectId", "name", "description", "startDate", "endDate" };
            string table = "Project";

            OleDbDataReader myReader;
            myReader = GetDataWhereInt(fields, table, "projectId", projectId);

            bool condition;
            condition = myReader.Read();

            while (condition)
            {
                int id = Convert.ToInt32(myReader["projectId"].ToString());
                string nm = myReader["name"].ToString();
                string desc = myReader["description"].ToString();
                DateTime start = Convert.ToDateTime(myReader["startDate"].ToString());
                DateTime end = Convert.ToDateTime(myReader["endDate"].ToString());

                newProject = new Project(id, nm, desc, start, end);
                break;
            }

            return newProject;
        }

        public void AddProject(string name, string description,
            DateTime startDate, DateTime endDate)
        {
            OleDbCommand myCommand = new OleDbCommand();
            myCommand.Connection = myConnection;
            myCommand.CommandText =
                "INSERT INTO Project(name, description, startDate, endDate) " +
                "VALUES (@name, @description, @startDate, @endDate)";
            myCommand.CommandType = CommandType.Text;
            myCommand.Parameters.AddWithValue("@name", name);
            myCommand.Parameters.AddWithValue("@description", description);
            myCommand.Parameters.AddWithValue("@startDate", startDate);
            myCommand.Parameters.AddWithValue("@endDate", endDate);
            myCommand.ExecuteNonQuery();
        }

        public void UpdateProject(int id, string name, string description,
                                  DateTime startDate, DateTime endDate)
        {
            OleDbCommand myCommand = new OleDbCommand();
            myCommand.Connection = myConnection;
            myCommand.CommandText =
                "UPDATE Project SET " +
                "name = '" + name + "', " +
                "description = '" + description + "', " +
                "startDate = #" + startDate.ToString("MM/dd/yyyy") + "#, " +
                "endDate = #" + endDate.ToString("MM/dd/yyyy") + "# " +
                "WHERE projectId = " + id + ";";
            myCommand.CommandType = CommandType.Text;
            myCommand.ExecuteNonQuery();
        }

        public void RemoveProject(int id)
        {
            OleDbCommand myCommand = new OleDbCommand();
            myCommand.Connection = myConnection;
            myCommand.CommandText = "DELETE FROM Project WHERE projectId = " + id;
            myCommand.CommandType = CommandType.Text;
            myCommand.ExecuteNonQuery();
        }

        /*public string GetProjectReport(int projectId)
        {
            Project p = GetProjectById(projectId);
            if (p == null) return "Project not found.";

            StringBuilder sb = new StringBuilder();
            sb.AppendLine("=== Project Report ===");
            sb.AppendLine($"ID:          {p.ProjectId}");
            sb.AppendLine($"Name:        {p.Name}");
            sb.AppendLine($"Description: {p.Description}");
            sb.AppendLine($"Start:       {p.StartDate:dd.MM.yyyy}");
            sb.AppendLine($"End:         {p.EndDate:dd.MM.yyyy}");
            sb.AppendLine();
            sb.AppendLine("--- User Stories ---");

            List<UserStory> stories = GetStoriesByProject(projectId);
            if (stories.Count == 0)
            {
                sb.AppendLine("  (no stories)");
            }
            else
            {
                foreach (UserStory s in stories)
                    sb.AppendLine($"  [{s.State}] (prio {s.Priority}) {s.Title}");
            }
            return sb.ToString();
        }*/


        // Class "Task" database methods:
        
            // addTask
            public void AddTask(int storyId, string title, string description, int priority, string labels)
                {
                    OleDbCommand myCommand = new OleDbCommand();
                
                    myCommand.Connection = myConnection;
                
                    myCommand.CommandText =
                        "INSERT INTO Task(storyId, title, description, priority, state, labels) VALUES (" +
                        storyId + ", '" +
                        title + "', '" +
                        description + "', " +
                        priority + ", 0, '" +
                        labels + "')";
                
                    myCommand.CommandType = CommandType.Text;
                
                    myCommand.ExecuteNonQuery();
                }
                
            // updateTask
            public void UpdateTask(int taskId, string title, string description, int priority)
                {
                    OleDbCommand myCommand = new OleDbCommand();
                
                    myCommand.Connection = myConnection;
                
                    myCommand.CommandText =
                        "UPDATE Task SET title='" + title +
                        "', description='" + description +
                        "', priority=" + priority +
                        " WHERE taskId=" + taskId;
                
                    myCommand.CommandType = CommandType.Text;
                
                    myCommand.ExecuteNonQuery();
                }

            // changeState
            
                public void ChangeTaskState(int taskId, TaskState newState)
                    {
                        OleDbCommand myCommand = new OleDbCommand();
                    
                        myCommand.Connection = myConnection;
                    
                        myCommand.CommandText =
                            "UPDATE Task SET state=" + (int)newState +
                            " WHERE taskId=" + taskId;
                    
                        myCommand.CommandType = CommandType.Text;
                    
                        myCommand.ExecuteNonQuery();
                    }

            // assignPerson
            public void AssignPersonToTask(int taskId, string personName)
                {
                    OleDbCommand myCommand = new OleDbCommand();
                
                    myCommand.Connection = myConnection;
                
                    myCommand.CommandText =
                        "UPDATE Task SET assignedPerson='" + personName +
                        "' WHERE taskId=" + taskId;
                
                    myCommand.CommandType = CommandType.Text;
                
                    myCommand.ExecuteNonQuery();
                }

            // removePerson
            public void RemovePersonFromTask(int taskId)
                {
                    OleDbCommand myCommand = new OleDbCommand();
                
                    myCommand.Connection = myConnection;
                
                    myCommand.CommandText =
                        "UPDATE Task SET assignedPerson=NULL WHERE taskId=" + taskId;
                
                    myCommand.CommandType = CommandType.Text;
                
                    myCommand.ExecuteNonQuery();
                }

            // getTaskReport
            public Task GetTaskById(int taskId)
                {
                    Task newTask = null;
                
                    string[] fields = { "taskId","storyId","title","description","priority","state","labels","assignedPerson" };
                
                    OleDbDataReader myReader;
                    myReader = GetDataWhereInt(fields,"Task","taskId",taskId);
                
                    if(myReader.Read())
                    {
                        int id = Convert.ToInt32(myReader["taskId"].ToString());
                        int storyId = Convert.ToInt32(myReader["storyId"].ToString());
                        string title = myReader["title"].ToString();
                        string desc = myReader["description"].ToString();
                        int prio = Convert.ToInt32(myReader["priority"].ToString());
                        TaskState state = (TaskState)Convert.ToInt32(myReader["state"].ToString());
                        string labels = myReader["labels"].ToString();
                        string person = myReader["assignedPerson"].ToString();
                
                        newTask = new Task(id,storyId,title,desc,prio,state,labels,person);
                    }
                
                    return newTask;
                }


        //Person classes
        public List<Person> GetAllPersons()
        {
            List<Person> personList = new List<Person>();

            string[] fields = { "personId", "personName", "PersonRole", "email", "stateId" };
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

            if (myReader.Read())
            {
                int id = Convert.ToInt32(myReader["PersonID"].ToString());
                string name = myReader["PersonName"].ToString();
                string role = myReader["PersonRole"].ToString();
                string email = myReader["Email"].ToString();

                newPerson = new Person(id, name, role, email);
            }

            return newPerson;
        }
        //Updated: remove id
        public void AddPerson(string name, string role, string email)
        {
            OleDbCommand myCommand = new OleDbCommand();

            myCommand.Connection = myConnection;

            //Updated: remove id
            myCommand.CommandText =
                "INSERT INTO Person(PersonName, PersonRole, Email) VALUES ('" + name + "', '" + role + "', '" + email + "')";

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

    

    // Team class
    
     public List<Team> GetAllTeams()
        {
            List<Team> teamList = new List<Team>();

            string[] fields = { "teamId", "name" };
            string table = "Team";

            OleDbDataReader myReader;
            myReader = GetData(fields, table);

            bool tCondition;
            tCondition = myReader.Read();

            while (tCondition)
            {
                int id = Convert.ToInt32(myReader["teamId"].ToString());
                string nm = myReader["name"].ToString();

                Team newT = new Team(id, nm);
                teamList.Add(newT);

                tCondition = myReader.Read();
            }

            return teamList;
        }




        public Team GetTeamById(int teamId)
        {
            Team newTeam = null;

            string[] fields = { "teamId", "name" };
            string table = "Team";

            OleDbDataReader myReader;
            myReader = GetDataWhereInt(fields, table, "teamId", teamId);

            bool tCondition;
            tCondition = myReader.Read();

            while (tCondition)
            {
                int id = Convert.ToInt32(myReader["teamId"].ToString());
                string nm = myReader["name"].ToString();

                newTeam = new Team(id, nm);
                break;
            }

            return newTeam;
        }


        public void AddTeam(int id, string name)
        {
            OleDbCommand myCommand = new OleDbCommand();
            myCommand.Connection = myConnection;
            myCommand.CommandText =
                "INSERT INTO Team(teamId, name) VALUES (@teamId, @name)";
            myCommand.CommandType = CommandType.Text;
            myCommand.Parameters.AddWithValue("@teamId", id);
            myCommand.Parameters.AddWithValue("@name", name);
            myCommand.ExecuteNonQuery();
        }

        public void RemoveTeam(int id)
        {
            OleDbCommand myCommand = new OleDbCommand();
            myCommand.Connection = myConnection;
            myCommand.CommandText = "DELETE FROM Team WHERE teamId = " + id;
            myCommand.CommandType = CommandType.Text;
            myCommand.ExecuteNonQuery();
        }
    }
}



    class MyApplication
    {
        DataService myDataService;

    public MyApplication()
    {
        myDataService = new DataService();
    }

    public void CreateProjecttTable()
    {
        myDataService.CreateProjecttTable();
    }

    public void CreateTeamTable()
    {
        myDataService.CreateTeamTable();
    }

    /*
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
       */
    //Project CLasses
    public List<Project> GetAllProjects()
    {
        return myDataService.GetAllProjects();
    }

    public Project GetProjectById(int id)
    {
        return myDataService.GetProjectById(id);
    }

    public void AddProject(string name, string description,
                       DateTime startDate, DateTime endDate)
    {
        myDataService.AddProject(name, description, startDate, endDate);
    }

    public void UpdateProject(int id, string name, string description,
                 DateTime startDate, DateTime endDate)
    {
        myDataService.UpdateProject(id, name, description, startDate, endDate);
    }

    public void RemoveProjectById(int id)
    {
        myDataService.RemoveProject(id);
    }


    // Task class
        public void AddTask(int storyId,string title,string description,int priority,string labels)
        {
            myDataService.AddTask(storyId,title,description,priority,labels);
        }
        
        public void UpdateTask(int taskId,string title,string description,int priority)
        {
            myDataService.UpdateTask(taskId,title,description,priority);
        }
        
        public void ChangeTaskState(int taskId,TaskState newState)
        {
            myDataService.ChangeTaskState(taskId,newState);
        }
        
        public void AssignPerson(int taskId,string person)
        {
            myDataService.AssignPersonToTask(taskId,person);
        }
        
        public void RemovePerson(int taskId)
        {
            myDataService.RemovePersonFromTask(taskId);
        }
        
        public Task GetTaskReport(int taskId)
        {
            return myDataService.GetTaskById(taskId);
        }

    //Person classes

        public List<Person> GetAllPersons()
        {
            return myDataService.GetAllPersons();
        }

        public Person GetPersonDataByName(string personName)
        {
            return myDataService.GetPersonByName(personName);
        }
        //Updated: remove id
        public void AddPerson(string name, string role, string email)
        {
            myDataService.AddPerson(name, role, email);
        }

        public void RemovePersonById(int id)
        {
            myDataService.RemovePerson(id);
        }

    //Team Class

    public List<Team> GetAllTeams()
    {
        return myDataService.GetAllTeams();
    }

    public Team GetTeamById(int id)
    {
        return myDataService.GetTeamById(id);
    }

    public void AddTeam(int id, string name)
    {
        myDataService.AddTeam(id, name);
    }

    public void RemoveTeamById(int id)
    {
        myDataService.RemoveTeam(id);
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


        //Project menu
        Console.WriteLine("7.  Show all projects");
        Console.WriteLine("8.  Show one project (by ID)");
        Console.WriteLine("9.  Add project");
        Console.WriteLine("10.  Edit project (by ID)");
        Console.WriteLine("11.  Remove project (by ID)");

        Console.WriteLine("17. Create Project table");
        Console.WriteLine("18. Create Team table");
        //Team Menu
        Console.WriteLine("12. Show all teams");
        Console.WriteLine("13. Show one team (by ID)");
        Console.WriteLine("14. Add team");
        Console.WriteLine("15. Remove team (by ID)");

        Console.WriteLine("exit (to finish)");
    }

    private void ShowListEnumerated(string[] stringList)
    {
        for (int i = 0; i < stringList.Length; i++)
            Console.WriteLine((i + 1) + ": " + stringList[i]);
    }

    /*private void ShowOneCustomer()
    {
        bool goOn = true, success;
        int custNr;
        while (goOn)
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
                        if (cust == null)
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

    }*/



    //Project classes

    private void ShowAllProjects()
    {
        Console.Clear();
        List<Project> projects = myApp.GetAllProjects();

        if (projects.Count == 0)
        {
            Console.WriteLine("No projects in database.");
            return;
        }

        foreach (Project p in projects)
            Console.WriteLine($"{p.ProjectId}: {p.Name}  [{p.StartDate:dd.MM.yyyy} - {p.EndDate:dd.MM.yyyy}]  {p.Description}");
    }

    private void ShowOneProject()
    {
        Console.Clear();
        try
        {
            Console.Write("Enter project ID: ");
            int id = Convert.ToInt32(Console.ReadLine());

            Project p = myApp.GetProjectById(id);
            if (p == null)
            {
                Console.WriteLine("Project not found.");
                return;
            }

            Console.WriteLine($"ID:          {p.ProjectId}");
            Console.WriteLine($"Name:        {p.Name}");
            Console.WriteLine($"Description: {p.Description}");
            Console.WriteLine($"Start:       {p.StartDate:dd.MM.yyyy}");
            Console.WriteLine($"End:         {p.EndDate:dd.MM.yyyy}");
        }
        catch
        {
            Console.WriteLine("Invalid input.");
        }
    }

    private void AddProject()
    {
        Console.Clear();
        try
        {
            Console.Write("Name: ");
            string name = Console.ReadLine();

            Console.Write("Description: ");
            string desc = Console.ReadLine();

            Console.Write("Start date (dd.MM.yyyy): ");
            DateTime start = DateTime.Parse(Console.ReadLine());

            Console.Write("End date (dd.MM.yyyy): ");
            DateTime end = DateTime.Parse(Console.ReadLine());

            myApp.AddProject(name, desc, start, end);
            Console.WriteLine("Project added.");
        }
        catch
        {
            Console.WriteLine("Failed to add project. Check your input.");
        }
    }

    private void UpdateProject()
    {
        Console.Clear();
        try
        {
            Console.Write("Project ID to edit: ");
            int id = Convert.ToInt32(Console.ReadLine());

            Project existing = myApp.GetProjectById(id);
            if (existing == null)
            {
                Console.WriteLine("Project not found.");
                return;
            }

            Console.Write($"New name [{existing.Name}]: ");
            string name = Console.ReadLine();
            if (string.IsNullOrWhiteSpace(name)) name = existing.Name;

            Console.Write($"New description [{existing.Description}]: ");
            string desc = Console.ReadLine();
            if (string.IsNullOrWhiteSpace(desc)) desc = existing.Description;

            Console.Write($"New start date [{existing.StartDate:dd.MM.yyyy}]: ");
            string startStr = Console.ReadLine();
            DateTime start = string.IsNullOrWhiteSpace(startStr)
                             ? existing.StartDate
                             : DateTime.Parse(startStr);

            Console.Write($"New end date [{existing.EndDate:dd.MM.yyyy}]: ");
            string endStr = Console.ReadLine();
            DateTime end = string.IsNullOrWhiteSpace(endStr)
                           ? existing.EndDate
                           : DateTime.Parse(endStr);

            myApp.UpdateProject(id, name, desc, start, end);
            Console.WriteLine("Project updated.");
        }
        catch
        {
            Console.WriteLine("Failed to edit project. Check your input.");
        }
    }

    private void RemoveProject()
    {
        Console.Clear();
        try
        {
            Console.Write("Project ID to remove: ");
            int id = Convert.ToInt32(Console.ReadLine());

            myApp.RemoveProjectById(id);
            Console.WriteLine("Removed (if existed).");
        }
        catch
        {
            Console.WriteLine("Failed to remove project.");
        }
    }

    /*private void ShowProjectReport()
     {
         Console.Clear();
         try
         {
             Console.Write("Project ID: ");
             int id = Convert.ToInt32(Console.ReadLine());
             Console.WriteLine(myApp.GetProjectReport(id));
         }
         catch
         {
             Console.WriteLine("Invalid input.");
         }
     }*/


    // Task class
        private void ShowTaskReport()
    {
        Console.Clear();
    
        Console.Write("Enter Task ID: ");
        int id = Convert.ToInt32(Console.ReadLine());
    
        Task t = myApp.GetTaskReport(id);
    
        if(t==null)
        {
            Console.WriteLine("Task not found.");
            return;
        }
    
        Console.WriteLine("Task ID: " + t.TaskId);
        Console.WriteLine("Title: " + t.Title);
        Console.WriteLine("Description: " + t.Description);
        Console.WriteLine("Priority: " + t.Priority);
        Console.WriteLine("State: " + t.State);
        Console.WriteLine("Labels: " + t.Labels);
        Console.WriteLine("Assigned Person: " + t.AssignedPerson);
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

    // Team Class

    private void ShowAllTeams()
    {
        Console.Clear();
        List<Team> teams = myApp.GetAllTeams();

        if (teams.Count == 0)
        {
            Console.WriteLine("No teams in database.");
            return;
        }

        foreach (Team t in teams)
            Console.WriteLine($"{t.TeamId}: {t.Name}");
    }

    private void ShowOneTeam()
    {
        Console.Clear();
        try
        {
            Console.Write("Enter team ID: ");
            int id = Convert.ToInt32(Console.ReadLine());

            Team t = myApp.GetTeamById(id);
            if (t == null)
            {
                Console.WriteLine("Team not found.");
                return;
            }

            Console.WriteLine($"ID:   {t.TeamId}");
            Console.WriteLine($"Name: {t.Name}");
        }
        catch
        {
            Console.WriteLine("Invalid input.");
        }
    }

    private void AddTeam()
    {
        Console.Clear();
        try
        {
            Console.Write("Team ID (int): ");
            int id = Convert.ToInt32(Console.ReadLine());

            Console.Write("Name: ");
            string name = Console.ReadLine();

            myApp.AddTeam(id, name);
            Console.WriteLine("Team added.");
        }
        catch
        {
            Console.WriteLine("Failed to add team. Check your input.");
        }
    }

    private void RemoveTeam()
    {
        Console.Clear();
        try
        {
            Console.Write("Team ID to remove: ");
            int id = Convert.ToInt32(Console.ReadLine());

            myApp.RemoveTeamById(id);
            Console.WriteLine("Removed (if existed).");
        }
        catch
        {
            Console.WriteLine("Failed to remove team.");
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
                /*case "1":
                    Console.Clear();
                    Console.Write(myApp.GetAllCustomers());
                    Console.WriteLine();
                    break;
                case "2":
                    Console.Clear();
                    ShowOneCustomer();
                    break; */

                //Project class

                //Project class

                case "7":
                    ShowAllProjects();
                    break;
                case "8":
                    ShowOneProject();
                    break;
                case "9":
                    AddProject();
                    break;
                case "10":
                    UpdateProject();
                    break;
                case "11":
                    RemoveProject();
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



                //Team Class

                case "12":
                    ShowAllTeams();
                    break;
                case "13":
                    ShowOneTeam();
                    break;
                case "14":
                    AddTeam();
                    break;
                case "15":
                    RemoveTeam();
                    break;

                case "17":
                    Console.Clear();
                    myApp.CreateProjecttTable();
                    break;

                case "18":
                    Console.Clear();
                    myApp.CreateTeamTable();
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


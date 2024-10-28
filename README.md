VIDEO DE EXPLICACION
https://youtu.be/6vs9aAU5VyU

Tarea: Implementar una Aplicación de Gestión de Tareas con MVC en Android

Objetivo: Crear una aplicación Android que siga el patrón de arquitectura MVC (Modelo, Vista, Controlador), 
permitiendo a los usuarios agregar, ver y eliminar tareas.


1. Archivo activity_main.xml
Este archivo define la estructura de la actividad principal de tu aplicación.

RelativeLayout: Es el contenedor principal que permite posicionar sus hijos de forma relativa entre sí.
FrameLayout: Este es el contenedor donde se cargarán los diferentes fragmentos de la aplicación. 
Usamos match_parent para que ocupe todo el espacio disponible.

![image](https://github.com/user-attachments/assets/4175704d-9961-4a44-9e51-751adcda5aa5)

<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">


    <FrameLayout
        android:id="@+id/fragment_container"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

</RelativeLayout>


2. Clase MainActivity

MainActivity: Es la actividad principal que maneja la lógica de la aplicación.
tasks: Lista mutable que almacena las tareas.

onCreate: Método que se llama cuando se crea la actividad. Aquí se carga el fragmento de la lista de tareas.

loadTaskListFragment: Carga el fragmento que muestra la lista de tareas.

loadAddTaskFragment: Carga el fragmento que permite agregar nuevas tareas. 

addToBackStack(null) permite que el usuario regrese a la pantalla anterior.

getTaskList, removeTask, addTask: Métodos para gestionar las tareas, incluyendo la adición y 
eliminación de tareas, y la actualización de la lista en el fragmento correspondiente.


![image](https://github.com/user-attachments/assets/a56a3cb4-311b-4447-b133-a50b8bd370e2)

![image](https://github.com/user-attachments/assets/92754939-37b1-4e0f-b418-7eae44ceac51)


package com.example.gestindetareasconmvc2

import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import androidx.fragment.app.Fragment

class MainActivity : AppCompatActivity() {
    private val tasks = mutableListOf<Task>()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)


        loadTaskListFragment()
    }

    // Método para cargar el fragmento de la lista de tareas
    fun loadTaskListFragment() {
        val taskListFragment = TaskListFragment()
        supportFragmentManager.beginTransaction()
            .replace(R.id.fragment_container, taskListFragment)
            .commit()
    }

    // Método para cargar el fragmento para agregar una tarea
    fun loadAddTaskFragment() {
        val addTaskFragment = AddTaskFragment()
        supportFragmentManager.beginTransaction()
            .replace(R.id.fragment_container, addTaskFragment)
            .addToBackStack(null)
            .commit()
    }

    fun getTaskList(): List<Task> {
        return tasks
    }

    fun removeTask(task: Task) {
        tasks.remove(task)

        val fragment = supportFragmentManager.findFragmentById(R.id.fragment_container) as? TaskListFragment
        fragment?.updateTaskList("all")
    }
    fun addTask(task: Task) {
        tasks.add(task)

        val fragment = supportFragmentManager.findFragmentById(R.id.fragment_container) as? TaskListFragment
        fragment?.updateTaskList("all")
    }

}


3. Clase AddTaskFragment

AddTaskFragment: Fragmento que permite al usuario ingresar el título y la descripción de una nueva tarea.

editTextTitle, editTextDescription: Campos de entrada para el título y descripción de la tarea.

onCreateView: Infla la vista del fragmento y establece el evento onClick para el botón de guardar.

saveTask: Verifica si los campos están llenos. Si están llenos, crea una nueva tarea y la añade a la lista en MainActivity.

loadFragment: Método para cargar otro fragmento (en este caso, TaskListFragment).


![image](https://github.com/user-attachments/assets/48e8b3ff-9a82-415e-9bc0-6931fea21984)

![image](https://github.com/user-attachments/assets/62a72e45-d857-41f7-a76d-bd73c6aa9116)


package com.example.gestindetareasconmvc2

import android.os.Bundle
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.Button
import android.widget.EditText
import android.widget.Toast
import androidx.fragment.app.Fragment

class AddTaskFragment : Fragment() {

    private lateinit var editTextTitle: EditText
    private lateinit var editTextDescription: EditText

    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {

        val view = inflater.inflate(R.layout.fragment_add_task, container, false)

        editTextTitle = view.findViewById(R.id.editTextTitle)
        editTextDescription = view.findViewById(R.id.editTextDescription)
        val btnSaveTask = view.findViewById<Button>(R.id.btnSaveTask)

        btnSaveTask.setOnClickListener {
            saveTask()
        }

        return view
    }

    private fun saveTask() {
        val title = editTextTitle.text.toString()
        val description = editTextDescription.text.toString()

        if (title.isEmpty() || description.isEmpty()) {
            Toast.makeText(context, "Por favor, complete todos los campos", Toast.LENGTH_SHORT).show()
        } else {

            val newTask = Task(title = title, description = description, completed = false)


            (activity as MainActivity).addTask(newTask)


            loadFragment(TaskListFragment())
        }
    }

    private fun loadFragment(fragment: Fragment) {
        val transaction = requireActivity().supportFragmentManager.beginTransaction()
        transaction.replace(R.id.fragment_container, fragment)
        transaction.addToBackStack(null)
        transaction.commit()
    }
}



4. Clase MainFragment

MainFragment: Fragmento que muestra la opción para agregar una nueva tarea.

onCreateView: Infla la vista del fragmento y establece el evento onClick para el botón que carga el AddTaskFragment.

![image](https://github.com/user-attachments/assets/3244b655-e5b5-4d24-ad5d-9f365d4683db)


package com.example.gestindetareasconmvc2

import android.os.Bundle
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import androidx.fragment.app.Fragment
import android.widget.Button

class MainFragment : Fragment() {

    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {

        val view = inflater.inflate(R.layout.fragment_main, container, false)

        val btnAddTask = view.findViewById<Button>(R.id.btnAddTask)
        btnAddTask.setOnClickListener {
            loadFragment(AddTaskFragment())
        }

        return view
    }

    private fun loadFragment(fragment: Fragment) {
        val transaction = requireActivity().supportFragmentManager.beginTransaction()
        transaction.replace(R.id.fragment_container, fragment)
        transaction.addToBackStack(null)
        transaction.commit()
    }
}


5. Clase Task

Task: Clase de datos que representa una tarea. Incluye un título, una descripción y un estado de completado.

![image](https://github.com/user-attachments/assets/613c1ed3-eab1-4fc4-ae1b-d0d37f7cd606)


package com.example.gestindetareasconmvc2

data class Task(
    val title: String,
    val description: String,
    var completed: Boolean = false
)


6. Clase TaskAdapter

TaskAdapter: Adaptador para mostrar la lista de tareas en un RecyclerView.

TaskViewHolder: Contenedor para los elementos de tarea. Contiene referencias a los componentes de la vista.

onCreateViewHolder: Infla el layout para cada elemento de tarea.

onBindViewHolder: Configura los datos de cada tarea en la vista correspondiente.

updateTasks: Actualiza la lista de tareas y notifica al RecyclerView.


![image](https://github.com/user-attachments/assets/9052d0aa-41df-4994-9e7c-8b992bea6a41)

![image](https://github.com/user-attachments/assets/1e9882df-aae1-440d-85cf-dbda14968fba)


package com.example.gestindetareasconmvc2

import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.CheckBox
import android.widget.ImageView
import android.widget.TextView
import androidx.recyclerview.widget.RecyclerView

class TaskAdapter(private var tasks: MutableList<Task>) : RecyclerView.Adapter<TaskAdapter.TaskViewHolder>() {

    class TaskViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
        val titleTextView: TextView = itemView.findViewById(R.id.task_title)
        val descriptionTextView: TextView = itemView.findViewById(R.id.task_description)
        val checkBox: CheckBox = itemView.findViewById(R.id.task_checkbox)
        val removeButton: ImageView = itemView.findViewById(R.id.remove_task_button)
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): TaskViewHolder {
        val view = LayoutInflater.from(parent.context).inflate(R.layout.item_task, parent, false)
        return TaskViewHolder(view)
    }

    override fun onBindViewHolder(holder: TaskViewHolder, position: Int) {
        val task = tasks[position]
        holder.titleTextView.text = task.title
        holder.descriptionTextView.text = task.description
        holder.checkBox.isChecked = task.completed


        holder.checkBox.setOnCheckedChangeListener { _, isChecked ->
            task.completed = isChecked
        }


        holder.removeButton.setOnClickListener {

            (holder.itemView.context as MainActivity).removeTask(task)
        }
    }

    override fun getItemCount(): Int {
        return tasks.size
    }

    fun updateTasks(newTasks: List<Task>) {
        tasks.clear()
        tasks.addAll(newTasks)
        notifyDataSetChanged()
    }
}


7. Clase TaskListFragment

TaskListFragment: Fragmento que muestra la lista de tareas utilizando un RecyclerView.

onCreateView: Infla la vista y configura el RecyclerView y el RadioGroup para filtrar las tareas.

updateTaskList: Actualiza la lista de tareas según el filtro seleccionado.


![image](https://github.com/user-attachments/assets/49ab8101-c4d3-4024-bc4a-64fec0239322)

![image](https://github.com/user-attachments/assets/538cc8a1-e16a-471d-b120-7ac36e4a4ae1)


package com.example.gestindetareasconmvc2

import android.os.Bundle
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.Button
import android.widget.RadioGroup
import androidx.fragment.app.Fragment
import androidx.recyclerview.widget.LinearLayoutManager
import androidx.recyclerview.widget.RecyclerView

class TaskListFragment : Fragment() {

    private lateinit var taskAdapter: TaskAdapter
    private lateinit var recyclerView: RecyclerView
    private lateinit var filterGroup: RadioGroup

    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        val view = inflater.inflate(R.layout.fragment_task_list, container, false)


        recyclerView = view.findViewById(R.id.recyclerView)
        recyclerView.layoutManager = LinearLayoutManager(context)


        taskAdapter = TaskAdapter(mutableListOf())
        recyclerView.adapter = taskAdapter


        val addButton: Button = view.findViewById(R.id.add_task_button)
        addButton.setOnClickListener {
            (activity as MainActivity).loadAddTaskFragment()
        }


        filterGroup = view.findViewById(R.id.filterGroup)
        filterGroup.setOnCheckedChangeListener { _, checkedId ->
            when (checkedId) {
                R.id.radio_all -> updateTaskList("all")
                R.id.radio_completed -> updateTaskList("completed")
                R.id.radio_not_completed -> updateTaskList("not_completed")
            }
        }


        updateTaskList("all")

        return view
    }

    // Método para actualizar la lista de tareas en función del filtro
    fun updateTaskList(filter: String) {
        val tasks = (activity as MainActivity).getTaskList()
        val filteredTasks = when (filter) {
            "completed" -> tasks.filter { it.completed }
            "not_completed" -> tasks.filter { !it.completed }
            else -> tasks
        }

        taskAdapter.updateTasks(filteredTasks)
    }
}


Fragmento para agregar tareas (fragment_add_task.xml)

Este layout se utiliza en el fragmento AddTaskFragment.

Contiene dos campos de texto (EditText) para que el usuario ingrese el título y la descripción de la tarea.

Un botón para guardar la tarea (btnSaveTask).


![image](https://github.com/user-attachments/assets/0ec4864a-f8cb-43fd-b689-0828f15a1344)


<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp">

    <EditText
        android:id="@+id/editTextTitle"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Título" />

    <EditText
        android:id="@+id/editTextDescription"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Descripción" />

    <Button
        android:id="@+id/btnSaveTask"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Guardar Tarea" />
</LinearLayout>


Fragmento principal (fragment_main.xml)

Este layout puede ser utilizado en la actividad principal para mostrar un botón que navegue al fragmento de agregar tareas.

Simple y funcional, solo incluye un botón para agregar tareas.

![image](https://github.com/user-attachments/assets/e0940180-e3d1-4919-83a2-d6487d65c87c)


<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <Button
        android:id="@+id/btnAddTask"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Agregar Tarea" />


</LinearLayout>


Lista de tareas (fragment_task_list.xml)


Este layout es para el TaskListFragment.

Incluye un TextView que muestra el título "Lista de Tareas".

Un botón para agregar tareas y un RadioGroup para filtrar las tareas (todas, completadas, no completadas).

Un RecyclerView para mostrar la lista de tareas.


![image](https://github.com/user-attachments/assets/e64cd143-43c5-45da-b120-014eb5a36236)

![image](https://github.com/user-attachments/assets/35f0634f-a2f1-48da-9a4b-272efe6e0954)


<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Lista de Tareas"
        android:textSize="18sp"
        android:textStyle="bold" />

    <Button
        android:id="@+id/add_task_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Agregar Tarea"
        android:layout_gravity="center_horizontal"
        android:layout_marginTop="16dp" />

    <RadioGroup
        android:id="@+id/filterGroup"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="horizontal">

        <RadioButton
            android:id="@+id/radio_all"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Todas"
            android:checked="true"/>

        <RadioButton
            android:id="@+id/radio_completed"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Completadas" />

        <RadioButton
            android:id="@+id/radio_not_completed"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="No Completadas" />
    </RadioGroup>


    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_marginTop="8dp" />
</LinearLayout>


Elemento de tarea (item_task.xml)
Este layout representa un elemento individual de tarea dentro del RecyclerView.

Incluye un CheckBox para marcar la tarea como completada, dos TextView para el título y la descripción de la tarea, 
y un ImageView para eliminar la tarea.


![image](https://github.com/user-attachments/assets/08f70b38-53f3-4cf2-9c25-d281e9bd981b)

![image](https://github.com/user-attachments/assets/50512a1f-258c-46d2-b301-2f7b1f036ab6)


<?xml version="1.0" encoding="utf-8"?>

<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="horizontal"
    android:padding="8dp">

    <CheckBox
        android:id="@+id/task_checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

    <TextView
        android:id="@+id/task_title"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:paddingStart="8dp"
        android:paddingEnd="8dp"
        android:textSize="16sp" />

    <TextView
        android:id="@+id/task_description"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="2"
        android:textSize="14sp" />

    <ImageView
        android:id="@+id/remove_task_button"
        android:layout_width="40dp"
        android:layout_height="40dp"
        android:src="@drawable/ic_delete"
        android:contentDescription="Eliminar tarea"
        android:padding="4dp" />
</LinearLayout>


Y por ultimo este es el resultado final 

![MVC](https://github.com/user-attachments/assets/b3660e09-e2d1-48d0-ad28-ccf010cccc3a)



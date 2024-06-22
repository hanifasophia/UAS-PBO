from tkinter import*
from tkinter import ttk, messagebox
from PIL import Image, ImageTk
import mysql.connector

def main():
    win=Tk()
    app=Login_Window(win)
    win.mainloop()

#==================================== L O G I N ==================================
class Login_Window:
    def __init__(self, root):
        self.root = root
        self.root.title("Login")
        self.root.geometry("1550x800+0+0") 

        # Background Image 
        original_image = Image.open("greenbg.jpg")
        screen_width = root.winfo_screenwidth()
        screen_height = root.winfo_screenheight()
        scale = min(screen_width / original_image.width, screen_height / original_image.height)
        new_size = (int(original_image.width * scale), int(original_image.height * scale))
        resized_image = original_image.resize(new_size, Image.LANCZOS)
        self.bg = ImageTk.PhotoImage(resized_image)
        lbl_bg = Label(self.root, image=self.bg)
        lbl_bg.place(x=0, y=0, relwidth=1, relheight=1)

        # Frame
        frame_width = 340
        frame_height = 450
        center_x = (screen_width - frame_width) // 2
        center_y = (screen_height - frame_height) // 2 - 50  
        frame = Frame(self.root, bg="white")
        frame.place(x=center_x, y=center_y, width=frame_width, height=frame_height)

        # Image 
        img1 = Image.open("user.png")
        img1 = img1.resize((100, 100), Image.LANCZOS)
        self.photoimage1 = ImageTk.PhotoImage(img1)
        img_x = (frame_width - 100) // 2  
        img_y = 20  
        lblimg1 = Label(frame, image=self.photoimage1, bg="white", borderwidth=0)
        lblimg1.place(x=img_x, y=img_y)
        get_str = Label(frame, text="Get Started", font=("Helvetica", 20, "bold"), fg="black", bg="white")
        get_str.place(x=90,y=120)
        
        # Label for Username
        username=lbl=Label(frame,text="Username", font=("Helvetica",13, "bold"), fg="black", bg="white")
        username.place(x=45,y=165)
        self.txtuser=ttk.Entry(frame, font=("Helvetica",13,"bold"))
        self.txtuser.place(x=40,y=190,width=270)

        # Label for Password
        password=lbl=Label(frame,text="Password", font=("Helvetica",13, "bold"), fg="black", bg="white")
        password.place(x=45,y=235)
        self.txtpass=ttk.Entry(frame, font=("Helvetica",13,"bold"))
        self.txtpass.place(x=40,y=260,width=270)

        # Login Button
        loginbtn=Button(frame, command=self.login, text="Login", font=("Helvetica",13, "bold"), bd=3,relief=RIDGE,fg="white",bg="#4D869C", activeforeground="#4D869C", activebackground="#CDE8E5")
        loginbtn.place(x=110,y=310,width=120,height=35)

        # Register Button
        registerbtn=Button(frame, text="New User Register",command=self.register_window, font=("Helvetica",10, "bold"),borderwidth=0,relief=RIDGE,fg="black",bg="white",activeforeground="#4D869C", activebackground="white")
        registerbtn.place(x=15,y=370,width=160)

        # Forgot Password Button
        registerbtn=Button(frame, command=self.forgot_password_window, text="Forgot Password", font=("Helvetica",10, "bold"),borderwidth=0,relief=RIDGE,fg="black",bg="white",activeforeground="#4D869C", activebackground="white")
        registerbtn.place(x=10,y=395,width=160)

    def register_window(self):
        self.new_window=Toplevel(self.root)
        self.app=Register(self.new_window)

    def login(self):
        if self.txtuser.get()=="" or self.txtpass.get()=="":
            messagebox.showerror("Error","All Field Required!")
        elif self.txtuser.get()=="h" and self.txtpass.get()=="h":
            messagebox.showinfo("Success","Welcome Back to Sparkle Spree World")
        else:
            conn=mysql.connector.connect(host="localhost",user="root",password="",database="pbo")
            my_cursor=conn.cursor()
            my_cursor.execute("select * from register where email = %s and password %s", (
                                                                                           self.var_email.get(),
                                                                                           self.var_pass.get()
                                                                                         ))
            
            row=my_cursor.fetchone()
            if row != None:
                messagebox.showerror("Error", "Invalid Username or Password")
            else:
                messagebox.showinfo("Success","Welcome Back to Sparkle Spree World")
            conn.commit()
            conn.close()

    #================================= R E S E T  P A S S W O R D =====================================
    def reset_pass(self):
        if self.combo_security_Q.get()=="Select":
            messagebox.showerror("Error", "Select Security Question",parent=self.root2)
        elif self.txt_security.get()=="":
            messagebox.showerror("Error", "Please enter the answer",parent=self.root2)
        elif self.txt_newpass.get()=="":
            messagebox.showerror("Error", "Please enter the new password",parent=self.root2)
        else:
            conn=mysql.connector.connect(host="localhost",user="root",password="",database="pbo")
            my_cursor=conn.cursor()
            query("select * from register where email=%s and securityQ=%s and securityA=%s")
            value=(self.txtuser.get(),self.combo_security_Q.get(),self.txt_security)
            my_cursor.execute(query,value)
            row=my_cursor.fetchone()
            if row==None:
                messagebox.showerror("Error","please Enter Correct Answer")
            else:
                query=("update register set password=%s where email=%s")
                value=(self.txt_newpass.get(), self.txtuser.get())
                my_cursor.execute(query,value)  

                conn.commit()
                conn.close()
                messagebox.showinfo("Info","your Password has been reset, please login new password",parent=self.root2)
                self.root2.destroy()


    #================================= F O R G O T  P A S S W O R D =====================================
    def forgot_password_window(self):
        if self.txtuser.get()=="":
            messagebox.showerror("Error","Please Enter the email address to reset password")
        else:
            conn=mysql.connector.connect(host="localhost",user="root",password="",database="pbo")
            my_cursor=conn.cursor()
            query=("select * from register where email=%s")
            value=(self.txtuser.get(),)
            my_cursor.execute(query,value)
            row=my_cursor.fetchone()
            #print(row)
            if row==None:
                messagebox.showerror("My Error", "Please enter the valid username")
            else:
                conn.close()
                self.root2=Toplevel()
                self.root2.title("Forgot Password")
                self.root2.geometry("340x450+610+170")

                l=Label(self.root2,text="Forgot Password", font=("Helvetica",20,"bold"),fg="black")
                l.place(x=0,y=10,relwidth=1)
                security_Q=Label(self.root2,text="Select Security Question",font=("Helvetica",15,"bold"),fg="black")
                security_Q.place(x=50,y=80)

                self.combo_security_Q=ttk.Combobox(self.root2, font=("Helvetica",10,"bold"),state="readonly")
                self.combo_security_Q["values"]=("Select", "Your Birth Place", "Your Mother's Name", "Your Pet Name")
                self.combo_security_Q.place(x=50,y=110,width=250)
                self.combo_security_Q.current(0)

                security_A=Label(self.root2,text="Select Answer",font=("Helvetica",15,"bold"),fg="black")
                security_A.place (x=50,y=150) 
                self.txt_security=ttk.Entry(self.root2, font=("Helvetica",15,"bold"))
                self.txt_security.place(x=50, y=180,width=250)

                new_password=Label(self.root2,text="New Password",font=("Helvetica",15,"bold"),fg="black")
                new_password.place (x=50,y=220) 
                self.txt_newpass=ttk.Entry(self.root2,font=("Helvetica",15,"bold"))
                self.txt_newpass.place(x=50, y=250,width=250)

                btn=Button(self.root2,text="Reset",font=("Helvetica",15,"bold"),fg="white", bg="#4D869C")
                btn.place(x=100,y=290)


#================================= R E G I S T E R ===================================
class Register:
    def __init__(self,root):
        self.root=root
        self.root.title("Register")
        self.root.geometry("1600x900+0+0")

        # Variables
        self.var_fname=StringVar()
        self.var_lname=StringVar()
        self.var_contact=StringVar()
        self.var_email=StringVar()
        self.var_securityQ=StringVar()
        self.var_securityA=StringVar()
        self.var_pass=StringVar()
        self.var_confpass=StringVar()

        # Background Image
        original_image = Image.open("greenbg.jpg")
        screen_width = root.winfo_screenwidth()
        screen_height = root.winfo_screenheight()
        scale = min(screen_width / original_image.width, screen_height / original_image.height)
        new_size = (int(original_image.width * scale), int(original_image.height * scale))
        resized_image = original_image.resize(new_size, Image.LANCZOS)
        self.bg = ImageTk.PhotoImage(resized_image)
        lbl_bg = Label(self.root, image=self.bg)
        lbl_bg.place(x=0, y=0, relwidth=1, relheight=1)

        # Frame 
        frame_width = 800
        frame_height = 500
        center_x = (screen_width - frame_width) // 2
        center_y = (screen_height - frame_height) // 2 - 50 
        frame = Frame(self.root, bg="white")
        frame.place(x=center_x, y=center_y, width=frame_width, height=frame_height)

        register_lbl=Label(frame,text="Register Here",font=("Helvetica",22,"bold"),fg="black",bg="white")
        register_lbl.place(x=20,y=20)

        # Label & Entry 

        #-------row 1-------
        fname=Label(frame, text="First Name",font=("Helvetica",14,"bold"),fg="black",bg="white")
        fname.place(x=50,y=100)
        fname_entry=ttk.Entry(frame, textvariable=self.var_fname,font=("Helvetica",14,"bold"))
        fname_entry.place(x=50,y=130,width=250)

        lname=Label(frame,text="Last Name",font=("Helvetica",15,"bold"),fg="black",bg="white")
        lname.place(x=370,y=100)
        self.txt_lname=ttk.Entry(frame, textvariable=self.var_lname, font=("Helvetica",15,"bold"))
        self.txt_lname.place(x=370,y=130,width=250)

        #-------row 2-------
        contact=Label(frame,text="Contact No",font=("Helvetica",15,"bold"),fg="black",bg="white")
        contact.place(x=50,y=170)
        self.txt_contact=ttk.Entry(frame, textvariable=self.var_contact, font=("Helvetica",15,"bold"))
        self.txt_contact.place(x=50,y=200,width=250)

        email=Label(frame,text="Email",font=("Helvetica",15,"bold"),fg="black",bg="white")
        email.place(x=370,y=170)
        self.txt_email=ttk.Entry(frame, textvariable=self.var_email, font=("Helvetica",15,"bold"))
        self.txt_email.place(x=370,y=200,width=250)

        #-------row 3-------
        security_Q=Label(frame,text="Select Security Question",font=("Helvetica",15,"bold"),fg="black",bg="white")
        security_Q.place(x=50,y=240)

        self.combo_security_Q=ttk.Combobox(frame, textvariable=self.var_securityQ, font=("Helvetica",10,"bold"),state="readonly")
        self.combo_security_Q["values"]=("Select", "Your Birth Place", "Your Mother's Name", "Your Pet Name")
        self.combo_security_Q.place(x=50,y=270, width=250)
        self.combo_security_Q.current(0)

        security_A=Label(frame,text="Select Answer",font=("Helvetica",15,"bold"),fg="black",bg="white")
        security_A.place(x=370,y=240)
        self.txt_security=ttk.Entry(frame,  textvariable=self.var_securityA, font=("Helvetica",15,"bold"))
        self.txt_security.place(x=370,y=270,width=250)

        #-------row 4-------
        pswd=Label(frame,text="Password",font=("Helvetica",15,"bold"),fg="black",bg="white")
        pswd.place(x=50,y=310)
        self.txt_pswd=ttk.Entry(frame,  textvariable=self.var_pass, font=("Helvetica",15))
        self.txt_pswd.place(x=50,y=340,width=250)

        confirm_pswd=Label(frame,text="Confirm Password",font=("Helvetica",15,"bold"),fg="black",bg="white")
        confirm_pswd.place(x=370,y=310)
        self.txt_confirm_pswd=ttk.Entry(frame, textvariable=self.var_confpass, font=("Helvetica",15))
        self.txt_confirm_pswd.place(x=370,y=340,width=250)

        # Checklist
        self.var_check=IntVar()
        self.checkbtn=Checkbutton(frame, variable=self.var_check, text="I Agree The Terms & Condiditon",font=("Helvetica",10,"bold"),fg="black",bg="white", onvalue=1, offvalue=0)
        self.checkbtn.place(x=50,y=380)

        # Buttons
        regisbtn=Button(frame,command=self.register_data,text="Register Now", font=("Helvetica",13, "bold"), bd=3,relief=RIDGE,fg="white",bg="#4D869C", activeforeground="#4D869C", activebackground="#CDE8E5")
        regisbtn.place(x=50,y=420,width=130,height=35)

        lgnbtn=Button(frame, text="Login", command=self.return_login, font=("Helvetica",13, "bold"), bd=3,relief=RIDGE,fg="white",bg="#4D869C", activeforeground="#4D869C", activebackground="#CDE8E5")
        lgnbtn.place(x=370,y=420,width=120,height=35)

    # Function Declaration
    def register_data(self):
        if self.var_fname.get()=="" or self.var_lname.get()=="" or self.var_contact.get()=="" or self.var_email.get()=="" or self.var_securityQ.get()=="Select" or self.var_securityA.get()=="" :
            messagebox.showerror("Error", "All Fields Are Required!")
        elif self.var_pass.get()!=self.var_confpass.get():
            messagebox.showerror("Error", "Password and Confirm Password Must Be Same")
        elif self.var_check.get()==0:
            messagebox.showerror("Error","Please Agree The Terms and Condition")
        else:
            conn=mysql.connector.connect(host="localhost",user="root",password="",database="pbo")
            my_cursor=conn.cursor()
            query=("select * from register where email=%s")
            value=(self.var_email.get(),)
            my_cursor.execute(query,value)
            row=my_cursor.fetchone()
            if row != None:
                messagebox.showerror("Error","User Already Exist, Please Try Another Email")
            else:
                my_cursor.execute("insert into register values(%s,%s,%s,%s,%s,%s,%s)",(

                                                                                        self.var_fname.get(),
                                                                                        self.var_lname.get(),
                                                                                        self.var_contact.get(),
                                                                                        self.var_email.get(),
                                                                                        self.var_securityQ.get(),
                                                                                        self.var_securityA.get(),
                                                                                        self.var_pass.get(),
                                    
                                                                                      ))
            conn.commit()
            conn.close()
            messagebox.showinfo("Success", "Register Successful")

    def return_login(self):
        self.root.destroy()

if __name__ == "__main__":
    main()
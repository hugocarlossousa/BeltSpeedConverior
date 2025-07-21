#Bibliotecas Utilizadas
from kivy.lang import Builder
from kivy.core.window import Window
from kivymd.uix.screen import MDScreen 
from kivymd.uix.screenmanager import MDScreenManager
from kivymd.app import MDApp
from kivymd.uix.dialog import MDDialog
from kivymd.uix.button import MDFlatButton
import sys

#interface KivyMD
KV = '''

#Tela Unica. 
<Tela1>

    name: 't1'

    #layout 
    MDFloatLayout:
        md_bg_color:('#DCDCDC')

        #Barra de titulo + função fechar o programa 
        MDTopAppBar:                
            title: "  BELT SPEED CONTROLLER"
            font_size: .03
            right_action_items: [["close", lambda x: root.fechar()]]
            md_bg_color: ("#4682B4")
            pos_hint: {'top': True}
        
        #box - utilizado para estetica da paagina
        MDBoxLayout:
            size_hint: 1, 0.08
            md_bg_color: ("#FFFAFA") 

        #logo do cliente/empresa 
        Image:
            source:"hsslogo.png"
            size_hint_x: 0.428
            pos_hint: {'center_x': 0.795,'center_y': 0.0465}               
        
        #Entrada de dados velocidade aferida na esteira
        MDTextField:
            id: tf1
            hint_text: "Measured Speed M/min"
            mode: "rectangle"
            size_hint_x: .70
            line_color_focus: "#4682B4"
            line_color_normal: "white"
            text_color_focus: "#4682B4"
            hint_text_color_normal:"#4682B4"
            hint_text_color_focus:"#708090"
            pos_hint: {'center_x': 0.5,'center_y': 0.78}

        #Entrada de dados velocidade desejada    
        MDTextField:
            id: tf2
            hint_text: "Target Speed M/min"
            mode: "rectangle"
            size_hint_x: .70
            line_color_focus: "#4682B4"
            line_color_normal: "white"
            text_color_focus: "#4682B4"
            hint_text_color_normal:"#4682B4"
            hint_text_color_focus:"#708090"
            pos_hint: {'center_x': 0.5,'center_y': 0.65}
        
        #Entrada de dados unidade pre estabelecida inicialmente no modulo
        MDTextField:
            id: tf3
            hint_text: "Seted module RPM/Hz"
            mode: "rectangle"
            size_hint_x: .70
            line_color_focus: "#4682B4"
            line_color_normal: "white"
            text_color_focus: "#4682B4"
            hint_text_color_normal:"#4682B4"
            hint_text_color_focus:"#708090"
            pos_hint: {'center_x': 0.5,'center_y': 0.52}
        
        #Botão Erase - limpa os dados em todas as entradas    
        MDIconButton:
            icon: "eraser"
            theme_icon_color: "Custom"
            icon_color:("#4682B4")
            icon_size: "45sp"  
            pos_hint: {'center_x': 0.39,'center_y': 0.40} 
            on_press:
                root.limpar()
        
        #Texto descritivo do botão Erase  
        MDLabel: 
            text: "Clear"
            color: ("#4682B4")
            font_size: 20
            halign: 'center'
            pos_hint: {'center_x': 0.39,'center_y': 0.35}

        #Botão Run - Inicia o programa principal
        MDIconButton:
            icon: "speedometer-slow"
            theme_icon_color: "Custom"
            icon_color:("#4682B4")
            icon_size: "45sp"  
            pos_hint: {'center_x': 0.61,'center_y': 0.40} 
            on_press:
                root.analisar()
        
        #Texto descritivo do botão Run 
        MDLabel: 
            text: "Run"
            color: ("#4682B4")
            font_size: 20
            halign: 'center'
            pos_hint: {'center_x': 0.61,'center_y': 0.35}
        
        #Saida de dados - retorna a string com os valores processados pelo programa principal.        
        MDLabel:
            id: resp 
            text: " "
            color: '#4682B4'
            font_size: 40
            halign: 'center'
            pos_hint: {'center_x': 0.5,'center_y': 0.21}
        

'''

#Root - Main Screen.
class Tela1(MDScreen):

    #Limpar os campos anteriormente preenchidos
    def limpar(self):
        self.ids.tf1.text = ''
        self.ids.tf2.text = ''
        self.ids.tf3.text = ''
        self.ids.resp.text = ''

    #Fechar a janela 
    def fechar(self):
        sys.exit()

    #Programa Principal   
    def analisar(self):
        #ENTRADAS 
        
        #Popup de Erro
        self.dialog = MDDialog(title = 'ERROR*', text = 'Invalid Velue!\nPlease, set a new valid value',
                                buttons = [MDFlatButton(text= 'OK',
                                on_release = self.liberar)])

        try:
            #Entrada - Velocidade de Aferida no transpotador
            r1= str(self.ids.tf1.text)
            conveyorSpeed= float(r1.replace(',','.'))
            #Entrada - Velocidade desejada
            r2= str(self.ids.tf2.text)
            targetSpeed= float(r2.replace(',','.'))
            #Entrada - Pre set RPM modulo
            r3= str(self.ids.tf3.text)
            moduleRpmSpeed= float(r3.replace(',','.'))

            #PROCESAMENTO
            #Caucula e converte a velocidade para RPM
            vf = moduleRpmSpeed/conveyorSpeed * targetSpeed

            #exporta os valores obtidos durante o calculo para o label resp
            self.ids.resp.text = ('SET THE PARAMETER IN MODULE\n {:0.2f} - RPM our Hz'.format(vf))
        except:
            self.dialog.open()
    
    #Função leração do pop pu erro
    def liberar(self, obj):
        
        self.dialog.dismiss()

# Classe de ativação
class SpeedHitterApp(MDApp):

    def build(self):
        Builder.load_string(KV)
        Window.size= [348, 587]
        sm = MDScreenManager()
        sm.add_widget(Tela1())   

        return sm

#inicia o programa.
SpeedHitterApp().run()

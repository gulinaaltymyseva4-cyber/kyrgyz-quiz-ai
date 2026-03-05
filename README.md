**
 * @license
 * SPDX-License-Identifier: Apache-2.0
 */

import React, { useState, useEffect, useCallback } from 'react';
import { motion, AnimatePresence } from 'motion/react';
import { 
  Trophy, 
  Settings, 
  Users, 
  Play, 
  Timer, 
  Coins, 
  ChevronRight, 
  RotateCcw,
  BrainCircuit,
  Loader2,
  CheckCircle2,
  XCircle,
  AlertCircle,
  Bell,
  Hash,
  Library,
  HelpCircle,
  X,
  Settings as SettingsIcon
} from 'lucide-react';
import { 
  Difficulty, 
  GameConfig, 
  GameStatus, 
  KYRGYZ_SUBJECTS, 
  Question, 
  Team,
  SavedQuiz,
  HelpMessage,
  NewsItem,
  Language
} from './types';
import { generateQuestions } from './services/geminiService';
import { cn } from './lib/utils';
import { TRANSLATIONS, LANGUAGES } from './translations';

// --- Components ---

const Header = ({ 
  onNavigate, 
  currentStatus,
  language
}: { 
  onNavigate: (status: GameStatus) => void,
  currentStatus: GameStatus,
  language: Language
}) => {
  const t = TRANSLATIONS[language];
  return (
    <header className="w-full py-6 px-8 flex flex-col items-center justify-center bg-white/10 backdrop-blur-md border-b border-white/20 relative z-20">
      <div className="w-full max-w-5xl flex justify-between items-center mb-4">
        <div className="flex gap-4 flex-wrap">
          <button 
            onClick={() => onNavigate("LIBRARY")}
            className={cn(
              "px-4 py-2 rounded-xl text-xs font-bold transition-all border flex items-center gap-2",
              currentStatus === "LIBRARY" ? "bg-emerald-500 border-emerald-400 text-white" : "bg-white/5 border-white/10 text-white/60 hover:bg-white/10"
            )}
          >
            <Library size={16} /> {t.library.toUpperCase()}
          </button>
          <button 
            onClick={() => onNavigate("HELP")}
            className={cn(
              "px-4 py-2 rounded-xl text-xs font-bold transition-all border flex items-center gap-2",
              currentStatus === "HELP" ? "bg-emerald-500 border-emerald-400 text-white" : "bg-white/5 border-white/10 text-white/60 hover:bg-white/10"
            )}
          >
            <HelpCircle size={16} /> {t.help.toUpperCase()}
          </button>
          <button 
            onClick={() => onNavigate("NEWS")}
            className={cn(
              "px-4 py-2 rounded-xl text-xs font-bold transition-all border flex items-center gap-2",
              currentStatus === "NEWS" ? "bg-emerald-500 border-emerald-400 text-white" : "bg-white/5 border-white/10 text-white/60 hover:bg-white/10"
            )}
          >
            <Bell size={16} /> {t.news.toUpperCase()}
          </button>
          <button 
            onClick={() => onNavigate("SETTINGS")}
            className={cn(
              "px-4 py-2 rounded-xl text-xs font-bold transition-all border flex items-center gap-2",
              currentStatus === "SETTINGS" ? "bg-emerald-500 border-emerald-400 text-white" : "bg-white/5 border-white/10 text-white/60 hover:bg-white/10"
            )}
          >
            <SettingsIcon size={16} /> {t.settings.toUpperCase()}
          </button>
          <button 
            onClick={() => onNavigate("PIN_ENTRY")}
            className={cn(
              "px-4 py-2 rounded-xl text-xs font-bold transition-all border flex items-center gap-2",
              currentStatus === "PIN_ENTRY" ? "bg-emerald-500 border-emerald-400 text-white" : "bg-white/5 border-white/10 text-white/60 hover:bg-white/10"
            )}
          >
            <Hash size={16} /> {t.pin.toUpperCase()}
          </button>
        </div>
        <button 
          onClick={() => onNavigate("SETUP")}
          className="px-4 py-2 rounded-xl text-xs font-bold bg-white/5 border border-white/10 text-white/60 hover:bg-white/10 transition-all"
        >
          🏠 {t.back.toUpperCase()}
        </button>
      </div>
      <div className="flex items-center gap-3 mb-2">
        <span className="text-2xl">🤖</span>
        <h2 className="text-xl font-bold text-white tracking-tight italic">Максатов Айдин</h2>
      </div>
      <h1 className="text-4xl font-black text-white uppercase tracking-widest drop-shadow-lg">
        {t.title}
      </h1>
    </header>
  );
};

const SetupView = ({ 
  config, 
  onConfigChange, 
  onStart,
  language
}: { 
  config: GameConfig, 
  onConfigChange: (config: GameConfig) => void, 
  onStart: (config: GameConfig) => void,
  language: Language
}) => {
  const t = TRANSLATIONS[language];
  return (
    <motion.div 
      initial={{ opacity: 0, y: 20 }}
      animate={{ opacity: 1, y: 0 }}
      className="max-w-5xl w-full grid grid-cols-1 md:grid-cols-2 gap-12 items-center"
    >
      {/* Left Column: Hero/Info */}
      <div className="flex flex-col justify-center space-y-8 p-4">
        <div className="inline-block px-4 py-1.5 bg-emerald-500/10 border border-emerald-500/20 rounded-full w-fit">
          <span className="text-emerald-400 text-xs font-bold uppercase tracking-widest">{t.version}</span>
        </div>
        <h2 className="text-6xl font-black text-white leading-tight">
          {t.heroTitle}
        </h2>
        <p className="text-white/60 text-xl leading-relaxed max-w-lg">
          {t.heroDesc}
        </p>
        <div className="flex gap-6 pt-4">
          <div className="flex items-center gap-3 text-white/50">
            <div className="w-10 h-10 bg-white/5 rounded-xl flex items-center justify-center border border-white/10">
              <Users className="w-5 h-5 text-emerald-400" />
            </div>
            <span className="text-sm font-medium">{t.teamBattle}</span>
          </div>
          <div className="flex items-center gap-3 text-white/50">
            <div className="w-10 h-10 bg-white/5 rounded-xl flex items-center justify-center border border-white/10">
              <Coins className="w-5 h-5 text-yellow-400" />
            </div>
            <span className="text-sm font-medium">{t.collectCoins}</span>
          </div>
        </div>
      </div>

      {/* Right Column: Config Card */}
      <div className="bg-white/10 backdrop-blur-xl p-8 rounded-[2.5rem] border border-white/20 shadow-2xl relative">
        <div className="absolute -top-6 -right-6 w-24 h-24 bg-emerald-500/20 blur-3xl rounded-full" />
        <div className="space-y-6 relative z-10">
          <div className="flex items-center justify-between mb-4">
            <h3 className="text-xl font-bold text-white">{t.setup}</h3>
            <div className="flex bg-black/40 p-1 rounded-xl border border-white/10">
              <button 
                onClick={() => onConfigChange({...config, useAI: true})}
                className={cn("px-4 py-2 rounded-lg text-[10px] font-black transition-all", config.useAI ? "bg-emerald-500 text-white shadow-lg shadow-emerald-500/20" : "text-white/40")}
              >
                {t.aiQuestions}
              </button>
              <button 
                onClick={() => onConfigChange({...config, useAI: false})}
                className={cn("px-4 py-2 rounded-lg text-[10px] font-black transition-all", !config.useAI ? "bg-emerald-500 text-white shadow-lg shadow-emerald-500/20" : "text-white/40")}
              >
                {t.ownQuestions}
              </button>
            </div>
          </div>

          {config.useAI ? (
            <>
              <div className="grid grid-cols-2 gap-4">
                <div className="space-y-2">
                  <label className="text-[10px] font-black text-emerald-300 uppercase tracking-widest">{t.subject}</label>
                  <select 
                    value={config.subject}
                    onChange={(e) => onConfigChange({ ...config, subject: e.target.value })}
                    className="w-full bg-black/40 border border-white/10 rounded-xl px-4 py-3 text-white focus:ring-2 focus:ring-emerald-500 outline-none transition-all appearance-none"
                  >
                    {KYRGYZ_SUBJECTS.map(s => <option key={s} value={s}>{s}</option>)}
                  </select>
                </div>
                <div className="space-y-2">
                  <label className="text-[10px] font-black text-emerald-300 uppercase tracking-widest">{t.difficulty}</label>
                  <select 
                    value={config.difficulty}
                    onChange={(e) => onConfigChange({ ...config, difficulty: e.target.value as Difficulty })}
                    className="w-full bg-black/40 border border-white/10 rounded-xl px-4 py-3 text-white focus:ring-2 focus:ring-emerald-500 outline-none transition-all appearance-none"
                  >
                    <option value="EASY">{t.easy}</option>
                    <option value="MEDIUM">{t.medium}</option>
                    <option value="HARD">{t.hard}</option>
                  </select>
                </div>
              </div>

              <div className="space-y-2">
                <label className="text-[10px] font-black text-emerald-300 uppercase tracking-widest">{t.topic}</label>
                <input 
                  placeholder={t.placeholderTopic}
                  value={config.topic}
                  onChange={(e) => onConfigChange({ ...config, topic: e.target.value })}
                  className="w-full bg-black/40 border border-white/10 rounded-xl px-4 py-3 text-white focus:ring-2 focus:ring-emerald-500 outline-none transition-all"
                />
              </div>
            </>
          ) : (
            <div className="space-y-4">
               <p className="text-xs text-white/40 italic">Өзүңүздүн суроолоруңузду кошуу үчүн төмөнкү баскычты колдонуңуз.</p>
               <button 
                 onClick={() => alert("Бул функция кийинки версияда толук ишке кирет!")}
                 className="w-full py-3 border border-dashed border-white/20 rounded-xl text-xs text-white/40 hover:bg-white/5 transition-all"
               >
                 + Суроо кошуу
               </button>
            </div>
          )}

          <div className="grid grid-cols-2 gap-4">
            <div className="space-y-2">
              <label className="text-[10px] font-black text-emerald-300 uppercase tracking-widest">{t.duration}</label>
              <input 
                type="number"
                value={config.duration}
                onChange={(e) => onConfigChange({ ...config, duration: parseInt(e.target.value) })}
                className="w-full bg-black/40 border border-white/10 rounded-xl px-4 py-3 text-white focus:ring-2 focus:ring-emerald-500 outline-none transition-all"
              />
            </div>
            <div className="space-y-2">
              <label className="text-[10px] font-black text-emerald-300 uppercase tracking-widest">{t.questionCount}</label>
              <input 
                type="number"
                value={config.questionCount}
                onChange={(e) => onConfigChange({ ...config, questionCount: parseInt(e.target.value) })}
                className="w-full bg-black/40 border border-white/10 rounded-xl px-4 py-3 text-white focus:ring-2 focus:ring-emerald-500 outline-none transition-all"
              />
            </div>
          </div>

          <button 
            onClick={() => onStart(config)}
            disabled={config.useAI ? !config.topic : false}
            className="w-full bg-emerald-500 hover:bg-emerald-400 disabled:opacity-50 text-white font-black py-5 rounded-2xl text-xl uppercase tracking-widest transition-all shadow-xl shadow-emerald-500/20 active:scale-95 flex items-center justify-center gap-3"
          >
            <Play fill="currentColor" size={20} /> {t.start}
          </button>
        </div>
      </div>
    </motion.div>
  );
};

const LibraryView = ({ 
  quizzes, 
  onSelect, 
  onDelete,
  language
}: { 
  quizzes: SavedQuiz[], 
  onSelect: (quiz: SavedQuiz) => void,
  onDelete: (id: string) => void,
  language: Language
}) => {
  const t = TRANSLATIONS[language];
  return (
    <motion.div 
      initial={{ opacity: 0, x: -20 }}
      animate={{ opacity: 1, x: 0 }}
      className="max-w-4xl w-full bg-white/10 backdrop-blur-xl p-8 rounded-3xl border border-white/20 shadow-2xl"
    >
      <div className="flex items-center gap-3 mb-8">
        <Trophy className="text-emerald-400 w-8 h-8" />
        <h2 className="text-3xl font-bold text-white">{t.myLibrary}</h2>
      </div>

      {quizzes.length === 0 ? (
        <div className="text-center py-12 text-white/40">
          <p className="text-xl italic">{t.emptyLibrary}</p>
        </div>
      ) : (
        <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
          {quizzes.map((quiz) => (
            <div 
              key={quiz.id}
              className="bg-black/40 border border-white/10 p-6 rounded-2xl hover:border-emerald-500/50 transition-all group"
            >
              <div className="flex justify-between items-start mb-4">
                <div>
                  <h3 className="text-xl font-bold text-white">{quiz.topic}</h3>
                  <p className="text-sm text-emerald-400">{quiz.subject}</p>
                </div>
                <button 
                  onClick={() => onDelete(quiz.id)}
                  className="text-red-500/40 hover:text-red-500 transition-colors"
                >
                  <XCircle size={20} />
                </button>
              </div>
              <div className="flex justify-between items-center">
                <span className="text-xs bg-white/5 px-3 py-1 rounded-full text-white/60">
                  {quiz.difficulty} • {quiz.questions.length} {t.question.toLowerCase()}
                </span>
                <button 
                  onClick={() => onSelect(quiz)}
                  className="bg-emerald-500 hover:bg-emerald-400 text-white px-4 py-2 rounded-xl text-sm font-bold transition-all"
                >
                  {t.play}
                </button>
              </div>
            </div>
          ))}
        </div>
      )}
    </motion.div>
  );
};

const HelpView = ({ 
  messages, 
  onSendMessage,
  language
}: { 
  messages: HelpMessage[], 
  onSendMessage: (text: string) => void,
  language: Language
}) => {
  const t = TRANSLATIONS[language];
  const [inputText, setInputText] = useState('');

  const handleSend = () => {
    if (!inputText.trim()) return;
    onSendMessage(inputText);
    setInputText('');
  };

  return (
    <motion.div 
      initial={{ opacity: 0, scale: 0.95 }}
      animate={{ opacity: 1, scale: 1 }}
      className="max-w-2xl w-full bg-white/10 backdrop-blur-xl p-8 rounded-3xl border border-white/20 shadow-2xl flex flex-col h-[600px]"
    >
      <div className="flex items-center gap-3 mb-8">
        <BrainCircuit className="text-emerald-400 w-8 h-8" />
        <h2 className="text-3xl font-bold text-white">{t.helpSupport}</h2>
      </div>

      <div className="flex-1 overflow-y-auto space-y-4 mb-6 pr-2 scrollbar-thin scrollbar-thumb-white/10">
        {messages.map((msg) => (
          <div 
            key={msg.id}
            className={cn(
              "max-w-[80%] p-4 rounded-2xl text-sm leading-relaxed",
              msg.sender === "user" 
                ? "bg-emerald-500 text-white ml-auto rounded-tr-none" 
                : "bg-white/10 text-white mr-auto rounded-tl-none border border-white/10"
            )}
          >
            {msg.text}
          </div>
        ))}
      </div>

      <div className="flex gap-2">
        <input 
          value={inputText}
          onChange={(e) => setInputText(e.target.value)}
          onKeyPress={(e) => e.key === 'Enter' && handleSend()}
          placeholder={t.helpPlaceholder}
          className="flex-1 bg-black/40 border border-white/20 rounded-xl px-4 py-3 text-white focus:ring-2 focus:ring-emerald-500 outline-none"
        />
        <button 
          onClick={handleSend}
          className="bg-emerald-500 hover:bg-emerald-400 text-white p-3 rounded-xl transition-all"
        >
          <ChevronRight />
        </button>
      </div>
    </motion.div>
  );
};

const SavePrompt = ({ 
  onConfirm, 
  onCancel,
  language
}: { 
  onConfirm: () => void, 
  onCancel: () => void,
  language: Language
}) => {
  const t = TRANSLATIONS[language];
  return (
    <motion.div 
      initial={{ opacity: 0, scale: 0.8 }}
      animate={{ opacity: 1, scale: 1 }}
      className="fixed inset-0 z-[100] flex items-center justify-center bg-black/60 backdrop-blur-md p-4"
    >
      <div className="bg-[#1e293b] border border-white/20 p-8 rounded-[2rem] max-w-sm w-full text-center shadow-2xl">
        <div className="w-20 h-20 bg-emerald-500/20 rounded-full flex items-center justify-center mx-auto mb-6">
          <RotateCcw className="text-emerald-400 w-10 h-10" />
        </div>
        <h3 className="text-2xl font-bold text-white mb-4">{t.saveToLibrary}</h3>
        <p className="text-white/60 mb-8">{t.saveConfirm}</p>
        <div className="flex gap-4">
          <button 
            onClick={onCancel}
            className="flex-1 py-4 rounded-xl bg-white/5 border border-white/10 text-white font-bold hover:bg-white/10 transition-all"
          >
            {t.no}
          </button>
          <button 
            onClick={onConfirm}
            className="flex-1 py-4 rounded-xl bg-emerald-500 text-white font-bold hover:bg-emerald-400 transition-all shadow-lg shadow-emerald-500/20"
          >
            {t.yes}
          </button>
        </div>
      </div>
    </motion.div>
  );
};

const PinEntryView = ({ 
  onJoin,
  language
}: { 
  onJoin: (pin: string) => void,
  language: Language
}) => {
  const t = TRANSLATIONS[language];
  const [pin, setPin] = useState('');

  return (
    <motion.div 
      initial={{ opacity: 0, scale: 0.9 }}
      animate={{ opacity: 1, scale: 1 }}
      className="max-w-md w-full bg-white/10 backdrop-blur-xl p-8 rounded-3xl border border-white/20 shadow-2xl text-center"
    >
      <div className="w-20 h-20 bg-emerald-500/20 rounded-full flex items-center justify-center mx-auto mb-6">
        <RotateCcw className="text-emerald-400 w-10 h-10" />
      </div>
      <h2 className="text-3xl font-bold text-white mb-4">{t.pin}</h2>
      <p className="text-white/60 mb-8">{t.enterPinDesc}</p>
      
      <input 
        type="text"
        maxLength={6}
        value={pin}
        onChange={(e) => setPin(e.target.value.replace(/\D/g, ''))}
        placeholder="000 000"
        className="w-full bg-black/40 border border-white/20 rounded-2xl px-6 py-5 text-4xl font-black text-center tracking-[0.3em] text-emerald-400 outline-none focus:ring-2 focus:ring-emerald-500 mb-8"
      />

      <button 
        onClick={() => onJoin(pin)}
        disabled={pin.length < 6}
        className="w-full bg-emerald-500 hover:bg-emerald-400 disabled:opacity-50 text-white font-black py-4 rounded-xl text-lg uppercase tracking-widest transition-all"
      >
        {t.join}
      </button>
    </motion.div>
  );
};

const NewsView = ({ 
  items,
  language
}: { 
  items: NewsItem[],
  language: Language
}) => {
  const t = TRANSLATIONS[language];
  return (
    <motion.div 
      initial={{ opacity: 0, y: 20 }}
      animate={{ opacity: 1, y: 0 }}
      className="max-w-3xl w-full bg-white/10 backdrop-blur-xl p-8 rounded-3xl border border-white/20 shadow-2xl"
    >
      <div className="flex items-center gap-3 mb-8">
        <ChevronRight className="text-emerald-400 w-8 h-8" />
        <h2 className="text-3xl font-bold text-white">{t.newsTitle}</h2>
      </div>

      <div className="space-y-6">
        {items.map((item) => (
          <div 
            key={item.id}
            className="bg-black/40 border border-white/10 p-6 rounded-2xl"
          >
            <div className="flex justify-between items-start mb-2">
              <h3 className="text-xl font-bold text-emerald-400">{item.title}</h3>
              <span className="text-xs text-white/40 font-mono">{item.date}</span>
            </div>
            <p className="text-white/80 leading-relaxed">{item.content}</p>
          </div>
        ))}
      </div>
    </motion.div>
  );
};

const TeamSetupView = ({ 
  onComplete, 
  pin,
  language
}: { 
  onComplete: (teams: [string, string]) => void,
  pin: string,
  language: Language
}) => {
  const t = TRANSLATIONS[language];
  const [team1, setTeam1] = useState(t.team1);
  const [team2, setTeam2] = useState(t.team2);

  return (
    <motion.div 
      initial={{ opacity: 0, scale: 0.9 }}
      animate={{ opacity: 1, scale: 1 }}
      className="max-w-md w-full bg-white/10 backdrop-blur-xl p-8 rounded-3xl border border-white/20 shadow-2xl text-center"
    >
      <div className="mb-8 p-4 bg-emerald-500/20 rounded-2xl border border-emerald-500/30">
        <p className="text-xs font-bold text-emerald-400 uppercase tracking-widest mb-1">{t.pinCode}</p>
        <p className="text-4xl font-black text-white tracking-[0.2em]">{pin}</p>
      </div>

      <Users className="w-16 h-16 text-emerald-400 mx-auto mb-6" />
      <h2 className="text-3xl font-bold text-white mb-8">{t.teamNames}</h2>
      
      <div className="space-y-6 mb-8">
        <div className="space-y-2 text-left">
          <label className="text-xs font-bold text-emerald-300 uppercase">{t.team1}</label>
          <input 
            value={team1}
            onChange={(e) => setTeam1(e.target.value)}
            className="w-full bg-black/40 border border-white/20 rounded-xl px-4 py-3 text-white focus:ring-2 focus:ring-emerald-500 outline-none"
          />
        </div>
        <div className="space-y-2 text-left">
          <label className="text-xs font-bold text-emerald-300 uppercase">{t.team2}</label>
          <input 
            value={team2}
            onChange={(e) => setTeam2(e.target.value)}
            className="w-full bg-black/40 border border-white/20 rounded-xl px-4 py-3 text-white focus:ring-2 focus:ring-emerald-500 outline-none"
          />
        </div>
      </div>

      <button 
        onClick={() => onComplete([team1, team2])}
        className="w-full bg-emerald-500 hover:bg-emerald-400 text-white font-black py-4 rounded-xl text-lg uppercase tracking-widest transition-all"
      >
        {t.start}
      </button>
    </motion.div>
  );
};

// --- Main Game Area ---

const CoinCollector = ({ 
  activeTeam, 
  onCollect,
  canCollect,
  language
}: { 
  activeTeam: number, 
  onCollect: () => void,
  canCollect: boolean,
  language: Language
}) => {
  const t = TRANSLATIONS[language];
  const [coins, setCoins] = useState<{ id: number, x: number, y: number }[]>([]);
  
  useEffect(() => {
    if (canCollect) {
      const newCoins = Array.from({ length: 5 }).map((_, i) => ({
        id: Date.now() + i,
        x: Math.random() * 80 + 10,
        y: Math.random() * 80 + 10,
      }));
      setCoins(newCoins);
    } else {
      setCoins([]);
    }
  }, [canCollect]);

  const handleCoinClick = (id: number) => {
    setCoins(prev => prev.filter(c => c.id !== id));
    onCollect();
  };

  return (
    <div className="relative w-full aspect-video bg-black/60 rounded-3xl border-4 border-white/10 overflow-hidden shadow-inner">
      <div className="absolute inset-0 bg-[radial-gradient(circle_at_center,_var(--tw-gradient-stops))] from-emerald-500/10 via-transparent to-transparent pointer-events-none" />
      
      <AnimatePresence>
        {coins.map((coin) => (
          <motion.button
            key={coin.id}
            initial={{ scale: 0, rotate: -180 }}
            animate={{ scale: 1, rotate: 0 }}
            exit={{ scale: 0, y: -50, opacity: 0 }}
            onClick={() => handleCoinClick(coin.id)}
            style={{ left: `${coin.x}%`, top: `${coin.y}%` }}
            className="absolute -translate-x-1/2 -translate-y-1/2 p-2"
          >
            <div className="w-12 h-12 bg-yellow-400 rounded-full border-4 border-yellow-600 flex items-center justify-center shadow-lg animate-bounce">
              <span className="text-yellow-800 font-black text-xl">₸</span>
            </div>
          </motion.button>
        ))}
      </AnimatePresence>

      {!canCollect && (
        <div className="absolute inset-0 flex items-center justify-center text-white/20 pointer-events-none">
          <div className="text-center">
            <Coins className="w-24 h-24 mx-auto mb-4 opacity-10" />
            <p className="text-2xl font-black uppercase tracking-widest">{t.coinField}</p>
            <p className="text-sm">{t.coinFieldDesc}</p>
          </div>
        </div>
      )}

      {canCollect && (
        <div className="absolute top-4 left-1/2 -translate-x-1/2 bg-emerald-500 text-white px-6 py-2 rounded-full font-black uppercase tracking-widest animate-pulse shadow-lg">
          {t.collectCoinsAction}
        </div>
      )}
    </div>
  );
};

const SettingsView = ({ 
  currentLanguage, 
  onLanguageChange,
  onBack
}: { 
  currentLanguage: Language, 
  onLanguageChange: (lang: Language) => void,
  onBack: () => void
}) => {
  const t = TRANSLATIONS[currentLanguage];
  return (
    <motion.div 
      initial={{ opacity: 0, scale: 0.95 }}
      animate={{ opacity: 1, scale: 1 }}
      className="max-w-2xl w-full bg-white/10 backdrop-blur-xl p-10 rounded-[3rem] border border-white/20 shadow-2xl"
    >
      <div className="flex items-center justify-between mb-10">
        <div className="flex items-center gap-4">
          <div className="w-12 h-12 bg-emerald-500/20 rounded-2xl flex items-center justify-center border border-emerald-500/30">
            <SettingsIcon className="text-emerald-400 w-6 h-6" />
          </div>
          <h2 className="text-3xl font-black text-white uppercase tracking-tighter">{t.settings}</h2>
        </div>
        <button 
          onClick={onBack}
          className="p-3 bg-white/5 hover:bg-white/10 rounded-2xl text-white/60 transition-all"
        >
          <X size={24} />
        </button>
      </div>

      <div className="space-y-8">
        <div className="space-y-4">
          <label className="text-xs font-black text-emerald-400 uppercase tracking-[0.2em]">{t.selectLanguage}</label>
          <div className="grid grid-cols-2 sm:grid-cols-3 gap-3">
            {LANGUAGES.map((lang) => (
              <button
                key={lang.code}
                onClick={() => onLanguageChange(lang.code as Language)}
                className={cn(
                  "flex items-center gap-3 p-4 rounded-2xl border transition-all",
                  currentLanguage === lang.code 
                    ? "bg-emerald-500 border-emerald-400 text-white shadow-lg shadow-emerald-500/20 scale-[1.02]" 
                    : "bg-black/40 border-white/10 text-white/60 hover:bg-white/5"
                )}
              >
                <span className="text-2xl">{lang.flag}</span>
                <span className="font-bold text-sm">{lang.name}</span>
              </button>
            ))}
          </div>
        </div>

        <div className="pt-6 border-t border-white/5">
          <button 
            onClick={onBack}
            className="w-full py-4 bg-white text-black font-black rounded-2xl uppercase tracking-widest hover:bg-emerald-400 hover:text-white transition-all"
          >
            {t.save}
          </button>
        </div>
      </div>
    </motion.div>
  );
};

export default function App() {
  const [status, setStatus] = useState<GameStatus>("SETUP");
  const [currentLanguage, setCurrentLanguage] = useState<Language>("ky");
  const [config, setConfig] = useState<GameConfig>({
    subject: KYRGYZ_SUBJECTS[0],
    topic: '',
    difficulty: 'MEDIUM',
    duration: 60,
    questionCount: 10,
    useAI: true
  });

  const [questions, setQuestions] = useState<Question[]>([]);
  const [currentQuestionIndex, setCurrentQuestionIndex] = useState(0);
  const [teams, setTeams] = useState<Team[]>([
    { id: 1, name: 'Team 1', score: 0, coins: 0 },
    { id: 2, name: 'Team 2', score: 0, coins: 0 }
  ]);
  const [activeTeamIndex, setActiveTeamIndex] = useState(0);
  const [timeLeft, setTimeLeft] = useState(60);
  const [isTimerActive, setIsTimerActive] = useState(false);
  const [showSavePrompt, setShowSavePrompt] = useState(false);
  const [isLoading, setIsLoading] = useState(false);
  const [library, setLibrary] = useState<SavedQuiz[]>(() => {
    const saved = localStorage.getItem('quiz_library');
    return saved ? JSON.parse(saved) : [];
  });
  const [helpMessages, setHelpMessages] = useState<HelpMessage[]>([
    { id: '1', text: TRANSLATIONS[currentLanguage].helpWelcome, sender: 'bot' }
  ]);
  const [news] = useState<NewsItem[]>([
    { id: '1', title: 'Жаңы версия 2.0', content: 'Биз ИИ аркылуу суроолорду түзүү функциясын коштук!', date: '05.03.2026' },
    { id: '2', title: 'Тыйын жыйноо', content: 'Эми туура жооп үчүн тыйын жыйнап, упайларды көбөйтсө болот.', date: '04.03.2026' }
  ]);
  const [gamePin, setGamePin] = useState('');

  useEffect(() => {
    localStorage.setItem('quiz_library', JSON.stringify(library));
  }, [library]);

  useEffect(() => {
    let timer: any;
    if (isTimerActive && timeLeft > 0) {
      timer = setInterval(() => setTimeLeft(prev => prev - 1), 1000);
    } else if (timeLeft === 0) {
      handleTimeUp();
    }
    return () => clearInterval(timer);
  }, [isTimerActive, timeLeft]);

  const handleTimeUp = () => {
    setIsTimerActive(false);
    alert(TRANSLATIONS[currentLanguage].timeUp);
    nextTurn();
  };

  const startGame = async (gameConfig: GameConfig) => {
    setConfig(gameConfig);
    if (gameConfig.useAI) {
      setIsLoading(true);
      try {
        const generated = await generateQuestions(
          gameConfig.subject,
          gameConfig.topic,
          gameConfig.difficulty,
          gameConfig.questionCount,
          currentLanguage
        );
        setQuestions(generated);
        setShowSavePrompt(true);
      } catch (error: any) {
        console.error("Game start error:", error);
        alert(`${TRANSLATIONS[currentLanguage].wrong}\n\nError: ${error.message}`);
      } finally {
        setIsLoading(false);
      }
    } else {
      setStatus("SETUP");
    }
  };

  const startBattle = (teamNames: [string, string]) => {
    setTeams([
      { id: 1, name: teamNames[0], score: 0, coins: 0 },
      { id: 2, name: teamNames[1], score: 0, coins: 0 }
    ]);
    setStatus("PLAYING");
    setCurrentQuestionIndex(0);
    setActiveTeamIndex(0);
    setTimeLeft(config.duration);
    setIsTimerActive(true);
  };

  const handleAnswer = (isCorrect: boolean) => {
    setIsTimerActive(false);
    if (isCorrect) {
      setTeams(prev => prev.map((t, i) => 
        i === activeTeamIndex ? { ...t, score: t.score + 10 } : t
      ));
      // Allow coin collection
    } else {
      nextTurn();
    }
  };

  const collectCoin = () => {
    setTeams(prev => prev.map((t, i) => 
      i === activeTeamIndex ? { ...t, coins: t.coins + 1, score: t.score + 5 } : t
    ));
  };

  const nextTurn = () => {
    if (currentQuestionIndex < questions.length - 1) {
      setCurrentQuestionIndex(prev => prev + 1);
      setActiveTeamIndex(prev => (prev === 0 ? 1 : 0));
      setTimeLeft(config.duration);
      setIsTimerActive(true);
    } else {
      setStatus("FINISHED");
    }
  };

  const saveQuiz = () => {
    const newQuiz: SavedQuiz = {
      id: Date.now().toString(),
      topic: config.topic || "Untitled",
      subject: config.subject,
      difficulty: config.difficulty,
      questions
    };
    setLibrary(prev => [...prev, newQuiz]);
    setShowSavePrompt(false);
    const pin = Math.floor(100000 + Math.random() * 900000).toString();
    setGamePin(pin);
    setStatus("TEAM_SETUP");
  };

  const loadQuiz = (quiz: SavedQuiz) => {
    setQuestions(quiz.questions);
    setConfig({
      ...config,
      topic: quiz.topic,
      subject: quiz.subject,
      difficulty: quiz.difficulty,
      questionCount: quiz.questions.length
    });
    const pin = Math.floor(100000 + Math.random() * 900000).toString();
    setGamePin(pin);
    setStatus("TEAM_SETUP");
  };

  const deleteQuiz = (id: string) => {
    setLibrary(prev => prev.filter(q => q.id !== id));
  };

  const handleHelpMessage = (text: string) => {
    const userMsg: HelpMessage = { id: Date.now().toString(), text, sender: 'user' };
    setHelpMessages(prev => [...prev, userMsg]);
    
    setTimeout(() => {
      const botMsg: HelpMessage = { 
        id: (Date.now() + 1).toString(), 
        text: "Бул суроо боюнча биздин адистер сизге жакында жооп берет. Рахмат!", 
        sender: 'bot' 
      };
      setHelpMessages(prev => [...prev, botMsg]);
    }, 1000);
  };

  return (
    <div className="min-h-screen bg-[#0f172a] text-white font-sans selection:bg-emerald-500/30 overflow-x-hidden">
      <Header 
        onNavigate={setStatus} 
        currentStatus={status} 
        language={currentLanguage} 
      />

      <main className="container mx-auto px-4 py-12 flex flex-col items-center justify-center min-h-[calc(100vh-180px)]">
        <AnimatePresence mode="wait">
          {isLoading && (
            <motion.div 
              key="loading"
              initial={{ opacity: 0 }}
              animate={{ opacity: 1 }}
              exit={{ opacity: 0 }}
              className="flex flex-col items-center gap-6"
            >
              <div className="relative">
                <Loader2 className="w-20 h-20 text-emerald-500 animate-spin" />
                <BrainCircuit className="w-10 h-10 text-white absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2" />
              </div>
              <div className="text-center space-y-2">
                <h3 className="text-2xl font-bold animate-pulse">{TRANSLATIONS[currentLanguage].generating}</h3>
                <p className="text-white/40 text-sm">ИИ суроолорду түзүп жатат, бир аз күтө туруңуз...</p>
              </div>
            </motion.div>
          )}

          {status === "SETUP" && !isLoading && (
            <SetupView 
              config={config} 
              onConfigChange={setConfig} 
              onStart={startGame} 
              language={currentLanguage}
            />
          )}

          {status === "LIBRARY" && (
            <LibraryView 
              quizzes={library} 
              onSelect={loadQuiz} 
              onDelete={deleteQuiz}
              language={currentLanguage}
            />
          )}

          {status === "HELP" && (
            <HelpView 
              messages={helpMessages} 
              onSendMessage={handleHelpMessage}
              language={currentLanguage}
            />
          )}

          {status === "NEWS" && (
            <NewsView 
              items={news}
              language={currentLanguage}
            />
          )}

          {status === "SETTINGS" && (
            <SettingsView 
              currentLanguage={currentLanguage}
              onLanguageChange={setCurrentLanguage}
              onBack={() => setStatus("SETUP")}
            />
          )}

          {status === "PIN_ENTRY" && (
            <PinEntryView 
              onJoin={(pin) => alert(`PIN ${pin} боюнча оюн табылган жок.`)}
              language={currentLanguage}
            />
          )}

          {status === "TEAM_SETUP" && (
            <TeamSetupView 
              onComplete={startBattle} 
              pin={gamePin}
              language={currentLanguage}
            />
          )}

          {status === "PLAYING" && questions.length > 0 && (
            <motion.div 
              key="playing"
              initial={{ opacity: 0, scale: 0.9 }}
              animate={{ opacity: 1, scale: 1 }}
              className="w-full max-w-4xl space-y-8"
            >
              {/* Game HUD */}
              <div className="grid grid-cols-3 gap-6">
                <div className={cn(
                  "p-6 rounded-3xl border-2 transition-all",
                  activeTeamIndex === 0 ? "bg-emerald-500/20 border-emerald-500 shadow-lg shadow-emerald-500/20 scale-105" : "bg-white/5 border-white/10 opacity-50"
                )}>
                  <p className="text-[10px] font-black uppercase tracking-widest text-emerald-400 mb-1">{teams[0].name}</p>
                  <div className="flex items-end gap-2">
                    <span className="text-4xl font-black">{teams[0].score}</span>
                    <span className="text-xs text-white/40 mb-1">PTS</span>
                  </div>
                  <div className="flex items-center gap-1 mt-2">
                    <Coins size={12} className="text-yellow-400" />
                    <span className="text-xs font-bold">{teams[0].coins}</span>
                  </div>
                </div>

                <div className="flex flex-col items-center justify-center bg-black/40 rounded-3xl border border-white/10 p-4">
                  <Timer className={cn("w-8 h-8 mb-2", timeLeft < 10 ? "text-red-500 animate-ping" : "text-white/40")} />
                  <span className={cn("text-3xl font-black font-mono", timeLeft < 10 ? "text-red-500" : "text-white")}>
                    {timeLeft}s
                  </span>
                </div>

                <div className={cn(
                  "p-6 rounded-3xl border-2 transition-all",
                  activeTeamIndex === 1 ? "bg-emerald-500/20 border-emerald-500 shadow-lg shadow-emerald-500/20 scale-105" : "bg-white/5 border-white/10 opacity-50"
                )}>
                  <p className="text-[10px] font-black uppercase tracking-widest text-emerald-400 mb-1">{teams[1].name}</p>
                  <div className="flex items-end gap-2">
                    <span className="text-4xl font-black">{teams[1].score}</span>
                    <span className="text-xs text-white/40 mb-1">PTS</span>
                  </div>
                  <div className="flex items-center gap-1 mt-2">
                    <Coins size={12} className="text-yellow-400" />
                    <span className="text-xs font-bold">{teams[1].coins}</span>
                  </div>
                </div>
              </div>

              {/* Question Card */}
              <div className="bg-white/10 backdrop-blur-xl p-10 rounded-[3rem] border border-white/20 shadow-2xl relative overflow-hidden">
                <div className="absolute top-0 left-0 w-full h-1 bg-white/5">
                  <motion.div 
                    className="h-full bg-emerald-500"
                    initial={{ width: "0%" }}
                    animate={{ width: `${((currentQuestionIndex + 1) / questions.length) * 100}%` }}
                  />
                </div>
                
                <div className="mb-8 flex justify-between items-center">
                  <span className="px-4 py-1 bg-emerald-500/20 text-emerald-400 rounded-full text-xs font-bold uppercase tracking-widest">
                    {TRANSLATIONS[currentLanguage].question} {currentQuestionIndex + 1} / {questions.length}
                  </span>
                  <span className="text-white/40 text-xs font-bold uppercase tracking-widest">
                    {config.subject} • {config.difficulty}
                  </span>
                </div>

                <h2 className="text-3xl font-bold text-white mb-10 leading-tight">
                  {questions[currentQuestionIndex].question}
                </h2>

                <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                  {questions[currentQuestionIndex].options.map((option, idx) => (
                    <button
                      key={idx}
                      onClick={() => handleAnswer(idx === questions[currentQuestionIndex].correctIndex)}
                      className="group relative bg-black/40 border border-white/10 p-6 rounded-2xl text-left hover:border-emerald-500 hover:bg-emerald-500/10 transition-all active:scale-[0.98]"
                    >
                      <div className="flex items-center gap-4">
                        <span className="w-8 h-8 rounded-lg bg-white/5 border border-white/10 flex items-center justify-center text-xs font-black group-hover:bg-emerald-500 group-hover:border-emerald-400 transition-all">
                          {String.fromCharCode(65 + idx)}
                        </span>
                        <span className="text-lg font-medium text-white/80 group-hover:text-white">{option}</span>
                      </div>
                    </button>
                  ))}
                </div>
              </div>

              {/* Interactive Area */}
              <CoinCollector 
                activeTeam={activeTeamIndex} 
                onCollect={collectCoin}
                canCollect={!isTimerActive && timeLeft > 0}
                language={currentLanguage}
              />

              {!isTimerActive && (
                <button 
                  onClick={nextTurn}
                  className="w-full py-4 bg-white text-black font-black rounded-2xl uppercase tracking-widest hover:bg-emerald-400 hover:text-white transition-all shadow-xl"
                >
                  {TRANSLATIONS[currentLanguage].next}
                </button>
              )}
            </motion.div>
          )}

          {status === "FINISHED" && (
            <motion.div 
              key="finished"
              initial={{ opacity: 0, scale: 0.8 }}
              animate={{ opacity: 1, scale: 1 }}
              className="max-w-2xl w-full bg-white/10 backdrop-blur-xl p-12 rounded-[3.5rem] border border-white/20 shadow-2xl text-center"
            >
              <Trophy className="w-24 h-24 text-yellow-400 mx-auto mb-8 animate-bounce" />
              <h2 className="text-5xl font-black text-white mb-4 uppercase tracking-tighter">
                {TRANSLATIONS[currentLanguage].gameOver}
              </h2>
              <p className="text-white/60 text-xl mb-12">{TRANSLATIONS[currentLanguage].finalScores}</p>

              <div className="grid grid-cols-2 gap-8 mb-12">
                <div className="p-8 bg-black/40 rounded-3xl border border-white/10">
                  <p className="text-xs font-bold text-emerald-400 uppercase tracking-widest mb-2">{teams[0].name}</p>
                  <p className="text-5xl font-black text-white">{teams[0].score}</p>
                  <div className="flex items-center justify-center gap-2 mt-2 text-yellow-400">
                    <Coins size={16} />
                    <span className="font-bold">{teams[0].coins}</span>
                  </div>
                </div>
                <div className="p-8 bg-black/40 rounded-3xl border border-white/10">
                  <p className="text-xs font-bold text-emerald-400 uppercase tracking-widest mb-2">{teams[1].name}</p>
                  <p className="text-5xl font-black text-white">{teams[1].score}</p>
                  <div className="flex items-center justify-center gap-2 mt-2 text-yellow-400">
                    <Coins size={16} />
                    <span className="font-bold">{teams[1].coins}</span>
                  </div>
                </div>
              </div>

              <div className="p-6 bg-emerald-500/20 rounded-3xl border border-emerald-500/30 mb-12">
                <h3 className="text-2xl font-bold text-emerald-400 mb-2">
                  {teams[0].score > teams[1].score ? teams[0].name : teams[1].score > teams[0].score ? teams[1].name : "Тең чыгуу!"}
                </h3>
                <p className="text-white/80 font-medium uppercase tracking-widest">{TRANSLATIONS[currentLanguage].winner}</p>
              </div>

              <button 
                onClick={() => setStatus("SETUP")}
                className="w-full bg-white text-black font-black py-5 rounded-2xl text-xl uppercase tracking-widest hover:bg-emerald-400 hover:text-white transition-all shadow-xl"
              >
                {TRANSLATIONS[currentLanguage].playAgain}
              </button>
            </motion.div>
          )}
        </AnimatePresence>
      </main>

      {showSavePrompt && (
        <SavePrompt 
          onConfirm={saveQuiz} 
          onCancel={() => {
            setShowSavePrompt(false);
            const pin = Math.floor(100000 + Math.random() * 900000).toString();
            setGamePin(pin);
            setStatus("TEAM_SETUP");
          }} 
          language={currentLanguage}
        />
      )}

      {/* Footer Info */}
      <footer className="w-full py-8 px-4 text-center border-t border-white/5 bg-black/20">
        <p className="text-white/20 text-xs font-medium uppercase tracking-[0.3em]">
          Тыйын-энмей — Кыргызстандын мектептери үчүн заманбап билим берүү платформасы.
        </p>
      </footer>
    </div>
  );
}
